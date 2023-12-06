---
pubDatetime: 2023-12-06T10:50:00Z
title: "Update（localization-management）：Organize text namespaces by modules"
postSlug: organize-text-namespaces-by-modules-in-localization-management 
# featured: true
draft: false
tags:
  - update
  - localization-management
ogImage: ""
description: ""
---

## Table of contents

## Background

- The localization management plugin synchronizes original texts by placing them in the same namespace (module). When encountering identical texts in different plugins, it removes duplicates and preserves the translation found in [`packages/core/client/src/locale`](https://github.com/nocobase/nocobase/tree/main/packages/core/client/src/locale) for further use.

- The NocoBase client creates an `i18n` instance with the option `fallbackNS: "client"` ([reference](https://github.com/nocobase/nocobase/blob/8983eed3308d018a309f1d39f5c6988bbc632878/packages/core/client/src/i18n/i18n.ts#L20)). Consequently, during plugin development, certain texts that already exist in `packages/core/client/src/locale` can be omitted from the plugin's locale files.

These two settings, however, present a issue. The same original texts can only be associated with one translation and can not differ across namespaces. For instance, if a user creates content with the same text as an existing translation, such as "Users" with the Chinese translation "用户," it will be displayed as "用户" in the "zh-CN" environment. This happens even if the user prefers to show the original text rather than the translation.

## Change

Translation texts are categorized based on their corresponding modules (menus, collections & fields, different plugins), with no deduplication. On the localization management page, texts can be filtered using a dropdown menu by selecting the corresponding module.

![](/content-static/2023-12-06-13-26-11.png)

## How to migrate

Follow the normal [upgrade process](https://docs.nocobase.com/welcome/getting-started/upgrading) to upgrade the system. Then switch to the corresponding language environment, and on the localization management page, synchronize translation texts by clicking the **"Sync"** button.

![](/content-static/2023-12-06-13-26-47.png)

## Breaking changes

### Translation texts are no longer deduplicated 

Duplicate texts across modules require separate translations.

![](/content-static/2023-12-06-13-28-16.png)

### Removed `fallbackNS` option from `i18n` instance 

The `i18n` instance of client no longer includes the option `fallbackNS: "client"`.   

For plugin developers, if language files in a plugin omit some texts already present in the core client, it's necessary to supplement them. If you still want to reuse content from `packages/core/client/src/locale`, pass the appropriate parameters when using it, for example:

```ts
import { useTranslation } from 'react-i18next';

export const NAMESPACE = 'localization-management';

export const useLocalTranslation = () => {
  return useTranslation([NAMESPACE, 'client'], { nsMode: 'fallback' });
};
```

