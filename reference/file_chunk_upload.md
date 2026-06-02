## Resumable Upload File

Refer to <https://manual.seafile.com/deploy_pro/web_resumable_upload.html> to learn more about web resumable upload.

### Check If Enable Resumable Upload

**GET** <http://192.168.1.113:8000/api/v2.1/repos/{repo_id}/file-uploaded-bytes/?parent_dir={parent_dir}&file_name={file_name}>

**Request parameters**

* repo_id
* parent_dir
* file_name

**Sample request**

```
curl -v -H 'Authorization: Token e71c00e93af863ba9bcddb61a46bb4de11d713fc' -H 'Accept: application/json; charset=utf-8; indent=4' "http://192.168.1.113:8000/api/v2.1/repos/09b7d3c0-5f0d-49be-9318-7ca136f386cd/file-uploaded-bytes/?parent_dir=/&file_name=test.md"

```

**Sample response**

```
*   Trying 192.168.1.113...
* Connected to 192.168.1.113 (192.168.1.113) port 8000 (#0)
> GET /api/v2.1/repos/09b7d3c0-5f0d-49be-9318-7ca136f386cd/file-uploaded-bytes/?parent_dir=/&file_name=test.md HTTP/1.1
> Host: 192.168.1.113:8000
> User-Agent: curl/7.50.1
> Authorization: Token e71c00e93af863ba9bcddb61a46bb4de11d713fc
> Accept: application/json; charset=utf-8; indent=4
>
* HTTP 1.0, assume close after body
< HTTP/1.0 200 OK
< Date: Fri, 01 Jun 2018 08:59:27 GMT
< Server: WSGIServer/0.1 Python/2.7.12+
< Content-Length: 26
< Content-Language: en
< Accept-Ranges: bytes
< Vary: Accept, Accept-Language, Cookie
< Allow: GET, HEAD, OPTIONS
< Content-Type: application/json; charset=utf-8; indent=4
<
{
    "uploadedBytes": 0
* Closing connection 0
}

```

If the response has this header `Accept-Ranges: bytes`, means that Seafile server supports resumable upload file.

**Errors**

* 400 parent_dir/file_name invalid.
* 404 Library/Folder not found.
* 500 Internal server error

### Get Upload Link

Same as getting upload link for uploading files.

### Get Bytes Already Uploaded

Before starting a resumable upload, you should first get how much of the file has been uploaded before, and use it as the offset to start uploading.

**GET** <http://192.168.1.113:8000/api/v2.1/repos/{repo_id}/file-uploaded-bytes/?parent_dir={parent_dir}&file_name={file_name}>

**Request parameters**

* repo_id
* parent_dir
* file_name

**Sample request**

```
curl -v -H 'Authorization: Token e71c00e93af863ba9bcddb61a46bb4de11d713fc' -H 'Accept: application/json; charset=utf-8; indent=4' "http://192.168.1.113:8000/api/v2.1/repos/09b7d3c0-5f0d-49be-9318-7ca136f386cd/file-uploaded-bytes/?parent_dir=/path-in-seafile-repo/&file_name=test.md"

```

**Sample response**

File has not been uploaded before.

```
{
    "uploadedBytes": 0
}

```

File has already been uploaded 149946368 bytes. If you want to continue uploading this file, upload it begin with 149946368 bytes.

```
{
    "uploadedBytes": 149946368
}

```

**Errors**

* 400 parent_dir/file_name invalid.
* 404 Library/Folder not found.
* 500 Internal server error

### Upload File in Chunks

Usually a large file is uploaded in many "chunks" with resumable upload. The chunk size can be chosen by the client app. The chunks are then uploaded one by one. After the last chunk is uploaded, the server will index the entire file and save it.

After getting the upload link and `uploadedBytes`, POST to this link for uploading the chunks.

**POST** <http://cloud.seafile.com:8082/upload-api/73c5d117-3bcf-48a0-aa2a-3f48d5274ae3>

**Request parameters**

The request parameters are the same as uploading a file. But two extra headers must be added in the request.

* Content-Range: specifies the range of bytes that are being uploaded. Refer to <https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Range> for more details.
* `Content-Disposition: attachment; filename=\"filename.txt\"` . When using resumable upload, the name of the file is no longer retrieved from the "file" field of the form. It's instead retrieved from a header.

Note: unlike uploading an entire file, resumable upload can only support uploading a single file in each request.

**Sample request**

upload file to `/path-in-seafile-repo/`, if a file named 'test.txt' already exists in `/path-in-seafile-repo/`, replace it with the new file:

```
curl -H "Content-Range: bytes 149946368-150994943/1587609600" -H "Content-Disposition: attachment; filename=\"test.md\"" -F file=@test.md -F parent_dir=/path-in-seafile-repo/ -F replace=1 http://cloud.seafile.com:8082/upload-api/73c5d117-3bcf-48a0-aa2a-3f48d5274ae3

```

* `149946368-150994943` means is now uploading 149946368-150994943 bytes.
* `1587609600` is file's total bytes.

**Sample response**

If this is not the last chunk, the server returns 200 with 

```
{
    "success": true
}

```

If this is the last chunk, the server returns the same response as uploading an entire file.

**Errors**

```
400 Bad request
403 Permission denied
440 Invalid filename
442 File size is too large
443 Out of quota
500 Internal server error

```
