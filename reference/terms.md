---
title: Terminology
excerpt: Here are some of the terms used in Seafile
category: 658bc87fd352d6004fe43f8f
isReference: true
slug: terms
---

## Users

- **user_id**: The unique user id in the form ...@auth.local, stored in EmailUser.email in ccnet_db. In old version, this is the same as a user's real email. In new version, user's real email is stored in contact_email field instead. It is unchangeable.
- **email**: Same as user_id. The term is still used in API's returned value. The term should be avoided in other places.
- **username**: Same as user_id, used internally in Seafile's code.
- **contact_email**: User's contact email, which is also a user's real email. It is changeable.




