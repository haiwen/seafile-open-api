---
title: Resumable File Upload
excerpt: Notes for resumable file upload.
category: 658bc87fd352d6004fe43f8f
isReference: true
slug: file_chunk_upload
---


## Resumable File Upload

This document describes how to upload a large file to Seafile in chunks.

Examples below assume the Seafile service is available at `http://{server}`.

### Workflow

1. Call the `file-uploaded-bytes` API to check whether resumable upload is supported and to get the current uploaded offset.
2. Get a normal upload link.
3. Upload the file chunk by chunk to the upload link with the required headers.

### Check Support and Uploaded Bytes

Use the following API before starting or resuming a chunked upload.

**GET** <http://{server}/api/v2.1/repos/{repo_id}/file-uploaded-bytes/?parent_dir={parent_dir}&file_name={file_name}>

**Request parameters**

* repo_id
* parent_dir
* file_name

**Sample request**

```bash
curl -v \
  -H 'Authorization: Token e71c00e93af863ba9bcddb61a46bb4de11d713fc' \
  -H 'Accept: application/json; charset=utf-8; indent=4' \
  "http://{server}/api/v2.1/repos/09b7d3c0-5f0d-49be-9318-7ca136f386cd/file-uploaded-bytes/?parent_dir=/path-in-seafile-repo/&file_name=test.md"
```

If the response contains the header `Accept-Ranges: bytes`, the Seafile server supports resumable upload.

The response body contains the number of bytes already stored on the server for this file.

**Sample response**

If the file has not been uploaded before:

```json
{
  "uploadedBytes": 0
}
```

If `149946368` bytes have already been uploaded:

```json
{
  "uploadedBytes": 149946368
}
```

Use `uploadedBytes` as the starting offset for the next chunk.

**Errors**

* 400 `parent_dir` or `file_name` is invalid.
* 404 Library or folder not found.
* 500 Internal server error.

### Get Upload Link

Get the upload link in the same way as a normal file upload. Then use that link for all chunk upload requests.

### Upload File in Chunks

A large file is uploaded as multiple chunks. The chunk size is chosen by the client. Chunks must be uploaded in order. After the last chunk is uploaded, the server indexes the full file and saves it.

After getting the upload link and `uploadedBytes`, POST each chunk to the upload link.

**POST** <http://{server}/seafhttp/upload-api/73c5d117-3bcf-48a0-aa2a-3f48d5274ae3>

**Request parameters**

The multipart form fields are the same as for a normal file upload, but chunked upload requires two extra headers:

* `Content-Range`: the byte range of the current chunk. Refer to <https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Range> for details.
* `Content-Disposition: attachment; filename="filename.txt"`: in chunked upload, the server reads the file name from this header instead of from the `file` form field.

Unlike a normal upload, chunked upload supports only one file per request.

**Sample request**

Upload a chunk to `/path-in-seafile-repo/`. If `test.md` already exists there, replace it.

```bash
curl \
  -H "Content-Range: bytes 149946368-150994943/1587609600" \
  -H "Content-Disposition: attachment; filename=\"test.md\"" \
  -F file=@test.md \
  -F parent_dir=/path-in-seafile-repo/ \
  -F replace=1 \
  http://{server}/seafhttp/upload-api/73c5d117-3bcf-48a0-aa2a-3f48d5274ae3
```

In this example:

* `149946368-150994943` is the byte range of the current chunk.
* `1587609600` is the total file size in bytes.

**Sample response**

If the uploaded chunk is not the last chunk, the server returns:

```json
{
  "success": true
}
```

If the uploaded chunk is the last chunk, the server returns the same response as a normal file upload.

**Errors**

* 400 Bad request.
* 403 Permission denied.
* 440 Invalid filename.
* 442 File size is too large.
* 443 Out of quota.
* 500 Internal server error.
