---
title: Authentication
excerpt: The authentication flows of SeaTable.
category: 658bc87fd352d6004fe43f8f
isReference: true
---

All API calls must be authenticated with a valid Seafile API key. 
All API-requests require an authorization header that looks like this, where different tokens can be used.
There are two ways to authenticate with the Seafile API: 

`Authorization: Bearer {{Account-Token, Library-Token}}`

## The two tokens

> Account-Token
>
> An **Account-Token** is generated when you log into Seafile. Generally, there is no need to generate an Account-Token manually.
> Seafile websites will automatically generate an Account-Token for you and store it in your browser's local storage. This token is valid until you log out.
> Almost your operations require an Account-Token.

> Library-Token
>
> A **Library-Token** is like a password to use the API requests of a single library. 
> You can create as many Library-Token per base as you want. Every Library-Token can have read or write permissions. This token is valid until you delete them.
> If the user only needs to manipulate the contents of a specific library, it is more secure to use the library API token.

The api key can be retrieved by the obtain auth api. See Quick Start below.  
[Quick start](https://seafile-api.readme.io/reference/post_api-v2-1-via-repo-token-sync-batch-move-item)