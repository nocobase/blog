---
author: Lin Chen
pubDatetime: 2023-11-13T02:36:25.037Z
title: "NocoBase 0.15：New plugin settings manager"
postSlug: release-v0.15
# featured: true
draft: false
tags:
  - release
ogImage: ""
description: ""
---

## Features

![Plugin settings manager](/content-static/psm.png)

## Breaking changes

### Plugin configuration page registration API

Previously, the `SettingsCenterProvider` was used to register the plugin configuration page, for example:

```tsx | pure
const HelloProvider = React.memo(props => {
  return (
    <SettingsCenterProvider
      settings={{
        hello: {
          title: "Hello",
          icon: "ApiOutlined",
          tabs: {
            tab1: {
              title: "Hello tab",
              component: HelloPluginSettingPage,
            },
          },
        },
      }}
    >
      {props.children}
    </SettingsCenterProvider>
  );
});
```

Now it needs to be changed to:

```tsx | pure
class HelloPlugin extends Plugin {
  async load() {
    this.app.pluginSettingsManager.add("hello", {
      title: "Hello",
      icon: "ApiOutlined",
      Component: HelloPluginSettingPage,
      // It is not necessary to pass this parameter if it is a new plugin
      aclSnippet: "pm.hello.tab1",
    });
  }
}
```

Get the routing information corresponding to the pluginSettingsManager

```tsx
const baseName = app.pluginSettingsManager.getRouteName('hello');
// admin.settings.hello
const basePath = app.pluginSettingsManager.getRoutePath('hello'); // /admin/settings.
// /admin/settings/hello
```

If there is a link jump inside the plugin configuration page, you need to change it accordingly, for example:

```tsx | pure
navigate('/admin/settings/hello/1').
navigate('/admin/settings/hello/2');

// This can be changed to
const basePath = app.pluginSettingsManager.getRoutePath('hello');
navigate(`${basePath}/1`);
navigate(`${basePath}/2`);
```

For more information, see the [plugin settings manager](https://docs.nocobase.com/development/client/plugin-settings).

## Changelog
For a complete changelog, please refer to [Changelog](https://github.com/nocobase/nocobase/blob/main/CHANGELOG.md).
