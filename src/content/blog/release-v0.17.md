---
pubDatetime: 2023-12-04T13:14:00Z
title: "NocoBase 0.17: New SchemaInitializer and SchemaSettings"
postSlug: release-v0.17
# featured: true
draft: false
tags:
  - release
ogImage: ""
description: ""
---

## New Features

In order to reduce development learning costs and provide a better front-end development experience, we have been refactoring the front-end kernel in phases over the past few months, including:

![nocobase-client](/content-static/nocobase-client.png)

This time v0.17 refactored the UI Schema Designer related SchemaInitializer and SchemaSettings

![](/content-static/SchemaInitializer.png)

![](/content-static/SchemaSettings.png)

In order to solve the problem of users' difficulty in getting started, we have also reorganized the documentation of each section

- [Plugin Development](https://docs-cn.nocobase.com/development) (fully revamped, released)
- [API Reference / Client](https://client.docs-cn.nocobase.com/core/application/application) (new section, already released)
- User's Manual (completely revamped, will be released in the next week or two)
- Plugin List (new block, includes all existing plugins' introduction, usage, and instructions for extension development, will be released in the next week or two)

## Breaking Changes

### Changes to SchemaInitializer

- Added `SchemaInitializerManager` for registering `SchemaInitializer`.
- Added `useSchemaInitializerRender()` instead of `render()` of `useSchemaInitializer()`.
- New `useSchemaInitializerItem()` to get the context of the current initialized item.
- New `SchemaInitializerItemGroup` component to be used as the default component for `type: 'itemGroup'`.
- New `SchemaInitializerSubMenu` component, used as default for `type: 'subMenu'`.
- New `SchemaInitializerDivider` component, used as default for `type: 'divider'`.
- New `SchemaInitializerChildren` component for custom rendering of multiple list items
- New `SchemaInitializerChild` component for custom rendering of a single list item
- Changed `SchemaInitializerContext` duty change to hold the current initializer context
- Changed `useSchemaInitializer()` responsibility to get the context of the current initializer
- Change `function SchemaInitializer` to `class SchemaInitializer` for defining the initializer
- Changed `SchemaInitializer` parameter.
  - New `name` mandatory parameter for `x-initializer` value.
  - New `Component` parameter for customized rendered buttons. Defaults to `SchemaInitializerButton`.
  - New `componentProps`, `style` for configuring `Component` properties and styles.
  - New `ItemsComponent` parameter for customizing the rendered list. Defaults to `SchemaInitializerItems`.
  - New `itemsComponentProps`, `itemsComponentStyle` for configuring properties and styles of `ItemsComponent`.
  - Added `popover` parameter to configure whether to show `popover` effect.
  - New `useInsert` parameter for when the `insert` function needs to use hooks.
  - Change Changed `dropdown` parameter to `popoverProps`, using `Popover` instead of `Dropdown`.
- Changed `items` parameter of `SchemaInitializer`.
  - Added `useChildren` function for dynamic control of child items.
  - Added `componentProps` function for component's own properties.
  - New `useComponentProps` function for dynamically handling component props.
  - Changed Changed the `key` parameter to `name`, which is used to uniquely identify the list item.
  - Changed changed `visible` parameter to `useVisible` function to dynamically control whether to display or not.
  - Changed the `component` parameter to `Component` for rendering list items.
- Changed `SchemaInitializer.Button` to `SchemaInitializerButton`, which is the default value for the Component parameter of the SchemaInitializer;
- Change `SchemaInitializer.Item` to `SchemaInitializerItem`, with no changes to the parameters;
- Change `SchemaInitializer.ActionModal` to `SchemaInitializerActionModal` with no change in parameters;
- Change `SchemaInitializer.SwitchItem` to `SchemaInitializer.Switch`, parameters unchanged.
- Remove `SchemaInitializerProvider` and replace with `SchemaInitializerManager`.
- Remove `SchemaInitializer.itemWrap`, no need to wrap the `item` component anymore;

### Changes to SchemaSettings

- Added `SchemaSettingsManager` for registering `SchemaSettings`.
- Added `useSchemaSettingsItem()`
- Added `useSchemaSettingsRender()`
- New `x-settings` parameter to configure the schema's settings.
- New `x-toolbar` parameter to configure the schema's toolbar.
- New `SchemaToolbar` component for customizing the schema's toolbar.
- New `useSchemaToolbarRender()`, replacing `useDesigner()`.
- Changed `function SchemaSettings` to `class SchemaSettings` for defining setters.
- Change `SchemaSettings` to `SchemaSettingsDropdown`.
- Changed `SchemaSettings.Item` to `SchemaSettingsItem`
- Changed `SchemaSettings.ItemGroup` to `SchemaSettingsItemGroup`
- Changed `SchemaSettings.SubMenu` to `SchemaSettingsSubMenu`
- Changed `SchemaSettings.Divider` to `SchemaSettingsDivider`
- Changed `SchemaSettings.Remove` to `SchemaSettingsRemove`
- Change `SchemaSettings.SelectItem` to `SchemaSettingsSelectItem`
- Changed `SchemaSettings.CascaderItem` to `SchemaSettingsCascaderItem`
- Change `SchemaSettings.SwitchItem` to `SchemaSettingsSwitchItem`
- Changed `SchemaSettings.ModalItem` to `SchemaSettingsModalItem`
- Change `SchemaSettings.ActionModalItem` to `SchemaSettingsActionModalItem`
- Remove `x-designer` parameter is deprecated and will be removed in the future, use `x-toolbar` instead.
- Remove `useDesigner()` deprecated and will be removed in the future, use `useSchemaToolbarRender()` instead.

See [Incompatible changes in NocoBase 0.17](https://docs.nocobase.com/welcome/release/upgrade-to/v017) for more details.
