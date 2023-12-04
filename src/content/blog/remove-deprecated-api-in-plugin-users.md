---
pubDatetime: 2023-12-04T00:55:00Z
title: "Note：Authentication-related APIs in Users plugin have been removed"
postSlug: remove-deprecated-api-in-plugin-users 
# featured: true
draft: false
tags:
  - deprecated
ogImage: ""
description: ""
---

In the v0.10 version released in June, the user authentication functionality in the User plugin(`@nocobase/plugin-users`) has been replaced by the Auth plugin(`@nocobase/plugin-auth`). The authentication-related APIs, like sign in and sign up, provided by the User plugin have been depreacted. For more details, refer to [NocoBase 0.10: New features in the second quarter#signin/signup api changes](/posts/release-v010#signinsignup-api-changes).

```bash
/api/users:signin -> /api/auth:signIn
/api/users:signup -> /api/auth:signUp
/api/users:signout -> /api/auth:signOut
/api/users:check -> /api/auth:check
```

The authentication-related APIs in the user plugin have now been removed, as detaild in <a href="https://github.com/nocobase/nocobase/pull/3122" target="_blank">PR-3122</a>.

