---
author: Lin Chen
pubDatetime: 2023-11-17T08:22:00Z
title: "NocoBase 0.16: New cache manager"
postSlug: release-v0.16
# featured: true
draft: false
tags:
  - release
ogImage: ""
description: ""
---

## New Features

The previous version of cache has poor usability (only support memory cache), v0.16 has been refactored, built-in memory and redis store, it also supports custom store. please refer to the [API documentation](https://docs.nocobase.com/api/cache/cache-manager) for the details of how to use.

## Breaking Changes

### Cache creation method update

Deprecated: Use `createCache` for cache creation.

```ts
import { createCache } from "@nocobase/cache";

const cache = createCache();
```

Cache now managed by `CacheManager` and created with `app.cacheManager`.

```ts
const cache = await app.cacheManager.createCache({
  name: "memory", // unique name of cache
  store: "memory", // unique name of cache method
  // other config
  max: 2000,
  ttl: 60 * 1000,
});
```

### Environment variables update

Previous environment variables of cache required a JSON string for configuring.

```bash
CACHE_CONFIG={"storePackage":"cache-manager-fs-hash","ttl":86400,"max":1000}
```

New environment variables for configuring cache:

```bash
# Unique name of default cache method, memory or redis
CACHE_DEFAULT_STORE=memory
# Max number of items in memory cache
CACHE_MEMORY_MAX=2000
# Redis，optional
CACHE_REDIS_URL=redis://localhost:6379
```
