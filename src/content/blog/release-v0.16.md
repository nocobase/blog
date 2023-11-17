---
author: Lin Chen
pubDatetime: 2023-09-11T08:22:00Z
title: "NocoBase 0.16: SchemaInitializer & SchemaSettings"
postSlug: release-v0.16
# featured: true
draft: false
tags:
  - release
ogImage: ""
description: ""
---

## Table of contents

## Breaking Changes

### Registration and Implementation of SchemaInitializer

#### Definition method

Previously, `SchemaInitializer` supported 2 definition methods, namely objects and components. For example:

```tsx | pure
const BlockInitializers = {
  title: '{{t("Add block")}}',
  icon: "PlusOutlined",
  items: [
    // ...
  ],
  // ...
};
```

```tsx | pure
const BlockInitializers = () => {
  return (
    <SchemaInitializer.Button
      title={'{{t("Add block")}}'}
      icon={"PlusOutlined"}
      items={
        [
          // ...
        ]
      }
      // ...
    />
  );
};
```

Now only the instance of `new SchemaInitializer()` is supported. For example:

```tsx | pure
const blockInitializers = new SchemaInitializer({
  name: "BlockInitializers", // 名称，和原来保持一致
  title: '{{t("Add block")}}',
  icon: "PlusOutlined",
  items: [
    // ...
  ],
  // ...
});
```

#### Parameters

In general, the parameters of `new SchemaInitializer()` refer to the previous object definition method, but there are new changes. Specifically as follows:

- Add `name` required parameter for `x-initializer` value.
- Add `Component` parameter for customized rendering of buttons. The default is `SchemaInitializerButton`.
- Add `componentProps`、`style` for configuring the properties and styles of `Component`.
- Add `ItemsComponent` parameter for customized rendering of lists. The default is `SchemaInitializerItems`.
- Add `itemsComponentProps`、`itemsComponentStyle` for configuring the properties and styles of `ItemsComponent`.
- Add `noPopover` parameter to configure whether to display the `popover` effect.
- Add `useInsert` parameter for when the `insert` function needs to use hooks.
- Change `dropdown` parameter to `popoverProps` and use `Popover` instead of `Dropdown`.
- items parameter changes
  - Add `useChildren` function to dynamically control the sub-items.
  - Add `componentProps` function for the properties of the component itself.
  - Change the `visible` parameter to `useVisible` function to dynamically control whether to display.
  - Change the `component` parameter to `Component` for rendering the list item.
  - Change the `key` parameter to `name` for the unique identification of the list item.

Example 1:

```diff
- export const BlockInitializers = {
+ export const blockInitializers = new SchemaInitializer({
+ name: 'BlockInitializers',
  'data-testid': 'add-block-button-in-page',
  title: '{{t("Add block")}}',
  icon: 'PlusOutlined',
  wrap: gridRowColWrap,
   items: [
    {
-     key: 'dataBlocks',
+     name: 'data-blocks',
      type: 'itemGroup',
      title: '{{t("Data blocks")}}',
      children: [
        {
-         key: 'table',
+         name: 'table',
-         type: 'item', // 当有 Component 参数时，就不需要此了
          title: '{{t("Table")}}',
-         component: TableBlockInitializer,
+         Component: TableBlockInitializer,
        },
         {
          key: 'form',
          type: 'item',
          title: '{{t("Form")}}',
          component: FormBlockInitializer,
        }
      ],
    },
  ],
});
```

Example 2:

```tsx | pure
export const BulkEditFormItemInitializers = (props: any) => {
  const { t } = useTranslation();
  const { insertPosition, component } = props;
  const associationFields = useAssociatedFormItemInitializerFields({
    readPretty: true,
    block: "Form",
  });
  return (
    <SchemaInitializer.Button
      data-testid="configure-fields-button-of-bulk-edit-form-item"
      wrap={gridRowColWrap}
      icon={"SettingOutlined"}
      items={[
        {
          type: "itemGroup",
          title: t("Display fields"),
          children: useCustomBulkEditFormItemInitializerFields(),
        },
        {
          type: "divider",
        },
        {
          type: "item",
          title: t("Add text"),
          component: BlockItemInitializer,
        },
      ]}
      insertPosition={insertPosition}
      component={component}
      title={component ? null : t("Configure fields")}
    />
  );
};
```

Now it needs to be changed to the way of `new SchemaInitializer()`:

```tsx | pure
const bulkEditFormItemInitializers = new SchemaInitializer({
  name: "BulkEditFormItemInitializers",
  "data-testid": "configure-fields-button-of-bulk-edit-form-item",
  wrap: gridRowColWrap,
  icon: "SettingOutlined",
  items: [
    {
      type: "itemGroup",
      title: t("Display fields"),
      name: "display-fields",
      useChildren: useCustomBulkEditFormItemInitializerFields,
    },
    {
      type: "divider",
    },
    {
      title: t("Add text"),
      name: "add-text",
      Component: BlockItemInitializer,
    },
  ],
});
```

The specific explanation of the parameters can refer to the type definition of `SchemaInitializer` and the [SchemaInitializer documentation](https://client.docs.nocobase.com/client/schema-initializer).

#### Implementation principle changes

Previously, `SchemaInitializer` was converted to a JSON object of `Menu` component for all `items`, and finally rendered as a Menu list.

Now the default is to render the `Component` component of the `items` list item, and how to render the `Component` component internally depends on itself, and finally it will not be spliced into a JSON object.

具体说明参考 `SchemaInitializer` 的 [Nested items 示例](https://client.docs.nocobase.com/client/schema-initializer#nested-items)。

#### Component parameter acquisition method

##### insert

Previously, the `insert` function was obtained through `props`, and now it needs to be obtained through `useSchemaInitializer()`. For example:

```diff
const FormBlockInitializer = (props) => {
-  const { insert } = props;
+  const { insert } = useSchemaInitializer();
 // ...
}

export const blockInitializers = new SchemaInitializer({
 name: 'BlockInitializers',
 items: [
  {
    name: 'form',
    Component: FormBlockInitializer
  }
 ]
});
```

##### Distinguish between common parameters and component properties

```ts
export const blockInitializers = new SchemaInitializer({
  name: "BlockInitializers",
  items: [
    {
      name: "form",
      Component: FormBlockInitializer,
      title: "Form",
      schema: {
        // ...
      },
      componentProps: {
        size: "mini",
      },
    },
  ],
});
```

The `title` and `schema` written in the outer layer are called common parameters, while the `size` written in `componentProps` is called component property. They are obtained in different ways, with common parameters obtained through the `useSchemaInitializerItem()` hook and component properties obtained through `props`. For example:

```tsx | pure
import { FC } from "react";
import { useSchemaInitializerItem } from "@nocobase/client";

interface FormBlockInitializerProps {
  size: string;
}
const FormBlockInitializer: FC<FormBlockInitializerProps> = props => {
  const { size } = props;
  const { title, schema } = useSchemaInitializerItem();
  // ...
};
```

#### Registration method

Previously, it was registered through `SchemaInitializerProvider`. For example:

```tsx | pure
<SchemaInitializerProvider
  initializers={{ BlockInitializers }}
  components={{ ManualActionDesigner }}
></SchemaInitializerProvider>
```

Now it needs to be changed to the way of `new SchemaInitializer()`:

```tsx | pure
import { Plugin } from "@nocobase/client";

class MyPlugin extends Plugin {
  async load() {
    this.app.schemaInitializerManager.add(blockInitializers);
    this.app.addComponents({ ManualActionDesigner });
  }
}
```

#### Modification method

以前是通过 `SchemaInitializerContext` 获取到全部的 `Initializers` 然后进行增删改。例如下面代码是为了往 `BlockInitializers` 中的 `media` 下添加 `Hello`：

```tsx | pure
const items = useContext<any>(SchemaInitializerContext);
const mediaItems = items.BlockInitializers.items.find(
  item => item.key === "media"
);

if (process.env.NODE_ENV !== "production" && !mediaItems) {
  throw new Error("media block initializer not found");
}

const children = mediaItems.children;
if (!children.find(item => item.key === "hello")) {
  children.push({
    key: "hello",
    type: "item",
    title: '{{t("Hello block")}}',
    component: HelloBlockInitializer,
  });
}
```

Now it is modified in a more concise way through the plugin. For example:

```tsx | pure
class MyPlugin extends Plugin {
  async load() {
    // get BlockInitializers
    const blockInitializers =
      this.app.schemaInitializerManager.get("BlockInitializers");

    // add Hello
    blockInitializers.add("media.hello", {
      title: '{{t("Hello block")}}',
      Component: HelloBlockInitializer,
    });
  }
}
```

#### Usage

Previously, rendering was done using `useSchemaInitializer`, but now it needs to be changed to `useSchemaInitializerRender`, and the `x-initializer-props` parameter needs to be added. For example:

```diff
- const { render } = useSchemaInitializer(fieldSchema['x-initializer']);
+ const { render } = useSchemaInitializerRender(fieldSchema['x-initializer'], fieldSchema['x-initializer-props']);

render();
render({ style: { marginLeft: 8 } })
```

More details can refer to the [SchemaInitializer documentation](https://client.docs.nocobase.com/client/schema-initializer).

### Registration and Implementation of SchemaSettings

Previously, `SchemaSettings` was written together with `Designer`, for example:

```tsx
const MyDesigner = props => {
  return (
    <div>
      {/* ... others */}
      <SchemaSettings
        title={
          <MenuOutlined
            role="button"
            aria-label={getAriaLabel("schema-settings")}
            style={{ cursor: "pointer", fontSize: 12 }}
          />
        }
      >
        <SchemaSettings.SwitchItem
          title={"Enable Header"}
          onClick={() => {}}
        ></SchemaSettings.SwitchItem>
        <SchemaSettings.Divider />
        <SchemaSettings.ModalItem
          title={"xxx"}
          schema={}
          onSubmit={props.onSubmit}
        ></SchemaSettings.ModalItem>
      </SchemaSettings>
      {/* ... others */}
    </div>
  );
};
```

Now it needs to be defined through `new SchemaSetting()`, for example:

```tsx
const mySettings = new SchemaSetting({
  name: "MySettings",
  items: [
    {
      name: "enableHeader",
      type: "switch",
      componentProps: {
        title: "Enable Header",
        onClick: () => {},
      },
    },
    {
      name: "divider",
      type: "divider",
    },
    {
      name: "xxx",
      type: "modal",
      useComponentProps() {
        const { onSubmit } = useSchemaDesigner();
        return {
          title: "xxx",
          schema: {},
          onSubmit,
        };
      },
    },
  ],
});
```

More details can refer to the [SchemaSettings documentation](https://client.docs.nocobase.com/apis/schema-settings)。

Then it needs to be registered to the App, for example:

```tsx
import { Plugin } from "@nocobase/client";

class MyPlugin extends Plugin {
  async load() {
    this.app.schemaSettingsManager.add(mySettings);
  }
}
```

Finally, use it in `Designer`, for example:

```diff
+import { useSchemaSettingsRender, SchemaDesignerProvider } from '@nocobase/client';

const MyDesigner = (props) => {
+  const { render } = useSchemaSettingsRender(
+    fieldSchema['x-settings'] || 'MySettings',
+    fieldSchema['x-settings-props'],
+  );
  return <div>
    {/* ... others */}
+    <SchemaDesignerProvider onSubmit={props.onSubmit}>
+     {render(props)}
+    </SchemaDesignerProvider>
-    <SchemaSettings title={
-      <MenuOutlined
-        role="button"
-        aria-label={getAriaLabel('schema-settings')}
-        style={{ cursor: 'pointer', fontSize: 12 }}
-      />
-    }>
-      <SchemaSettings.SwitchItem title={'Enable Header'} onClick={() => {}}></SchemaSettings.SwitchItem>
-      <SchemaSettings.Divider />
-      <SchemaSettings.ModalItem title={'xxx'} schema={} onSubmit={props.onSubmit}></SchemaSettings.ModalItem>
-    </SchemaSettings>
    {/* ... others */}
  </div>
}
```

More usage instructions please refer to [SchemaSettings](https://client.docs.nocobase.com/apis/schema-settings).

### Cache module (`@nocobase/cache`) usage update

After refactoring, the cache module now encapsulated based on [node-cache-manager](https://github.com/node-cache-manager/node-cache-manager), implementing a centralized cache manager that integrates both memory and Redis. Additionally, it allows for extending and adding other cache methods easily. For more detailed usage instructions, please refer to [API documentation](https://docs.nocobase.com/api/cache).

#### Cache creation method update

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

#### Environment variables update

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
