---
title: Authentication
excerpt: The authentication flows of Seafile.
category: 658bc87fd352d6004fe43f8f
isReference: true
slug: authentication
---

All API calls must be authenticated with a valid Seafile API token. 
All API-requests require an authorization header that looks like this, where different tokens can be used.
There are two ways to authenticate with the Seafile API: 

`Authorization: Bearer {{Account-Token, Repo-Token}}`

## The two tokens

#### Account-Token

An **Account-Token** is generated with your account name and password. Most APIs can be called with an account token.

#### Repo-Token

A **Repo-Token** is like a password to use the APIs of a single library. You can create as many Repo-Token per library as you want. Every Repo-Token can have read or write permissions. This token is valid until you delete them. If a user only needs to manipulate the contents of a specific library, it is more secure to use the repo API token.

You can generate **Repo-Token** for a library via the Web UI, in "Library context menu -> Advanced -> API Token".
