---
description: ' Debug PhysX issues for your game in Open 3D Engine. '
title: Debugging PhysX
weight: 1000
---

{{< preview-migrated >}}

The PhysX system has the following features that you can use to debug issues\.

**Note**
You must first enable the [PhysX Debug](/docs/user-guide/gems/physx-debug.md) gem\.

**Topics**
+ [PhysX Debug Console Variables](#debugging-physx-console-variables)
+ [Debugging with the ImGui Tool](#imgui-debugging-tool)
+ [Debug Options in the PhysX Configuration](#physx-debugging-configuration)

## PhysX Debug Console Variables {#debugging-physx-console-variables}

Enter the following console variables to debug your PhysX issues\.

Sets your preferences for debugging\. As a recommended best practice, enter this console variable command as your first step for debugging\.

**Example**

```
physx_Debug 1
```

You can specify the following values:
+ `1` - Enable debug visualizations\. By default, this value enables the collision shapes and edges for your PhysX entities\.
+ `2` - Enables all configuration options\. This enables all the available visualization options\.
+ `3` - Toggles the proximity based collider visualization\. This value applies only to mesh colliders\. See [Physics asset colliders](/docs/userguide/components/physx-collider#physics-asset-colliders)\.
+ `0` - Disables debug visualizations\.

Toggles a visual culling box frame\.

**Example**

```
physx_CullingBox 1
```

Adjusts the culling box size to **100**\. Enter **0** to disable culling\.

```
physx_CullingBoxSize 100
```

Connects to the PhysX Visual Debugger\. You must have the PhysX Visual Debugger open to run this command\. See [Debugger Configuration](/docs/user-guide/interactivity/physics/nvidia-physx/configuration-debugger.md)\.

```
physx_PvdConnect
```

Disconnects from the PhysX Visual Debugger\. You must have the PhysX Visual Debugger open to run this command\. See [Debugger Configuration](/docs/user-guide/interactivity/physics/nvidia-physx/configuration-debugger.md)\.

```
physx_PvdDisconnect
```

For more information, see [Using the Console Window](/docs/user-guide/editor/console.md)\.

## Debugging with the ImGui Tool {#imgui-debugging-tool}

In game mode, you can configure the PhysX debug settings using the immediate mode graphical user interface \(**ImGui**\) tool\.

**Note**
You must enable the ImGui gem to access this tool\. For more information, see [Enabling Gems](/docs/userguide/gems/using-project-configurator.md)\.

**To debug with the ImGui tool**

1. Press **Ctrl\+G** to enter gameplay mode\.

1. Press the **Home** key to open the **ImGui** tool\. The **PhysX Debug** menu appears under the **Perspective** viewport\.

1. Click **PhysX Debug**\.
**Example**
![\[PhysX Debug menu in gameplay mode.\]](/images/user-guide/physx/physx-debugger-imgui-tool.png)

1. You can make the following changes\.
****
[\[See the AWS documentation website for more details\]](/docs/userguide/debugging/physx)

## Debug Options in the PhysX Configuration {#physx-debugging-configuration}

You can also specify debug settings in the **PhysX Configuration** tool\. See [Debugger Configuration](/docs/user-guide/interactivity/physics/nvidia-physx/configuration-debugger.md)\.