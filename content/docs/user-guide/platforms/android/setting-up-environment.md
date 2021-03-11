---
description: ' Set up your Open 3D Engine environment to build your project for Android. '
linktitle: Environment setup
title: Set up your environment to develop for Android with O3DE
weight: 100
---

{{< preview-migrated >}}

 This section walks you through the steps needed to get your development environment and Open 3D Engine projects ready for build and deployment to Android\. To get started, make sure that you have the [Prerequisites](/docs/userguide/mobile/android/intro#android-prerequisites)\. To learn about the platform\-specific steps involved in project configuration and building, see [Configure O3DE projects for Android](/docs/user-guide/platforms/android/configure-project.md) and [Build and deploy your project for Android](/docs/user-guide/platforms/android/build-deploy.md)\.

**Topics**
- [Set up your environment to develop for Android with O3DE {#android-setting-up-environment}](#set-up-your-environment-to-develop-for-android-with-lumberyard)
  - [O3DE environment setup {#android-setting-up-environment-steps}](#lumberyard-environment-setup)
  - [Add Android tools to `PATH` {#android-setting-up-path}](#add-android-tools-to-path)
  - [Build system setup {#android-setting-build-system}](#build-system-setup)
  - [Next steps {#android-setting-up-next-steps}](#next-steps)

## O3DE environment setup {#android-setting-up-environment-steps}

 After you've [installed the prerequisites](/docs/userguide/mobile/android/intro#android-prerequisites), configure your O3DE installation to allow Android builds\.

**To allow Android builds**

1.  Start the O3DE Setup Assistant\. If you're installing O3DE for the first time, Setup Assistant starts automatically\. Otherwise, launch it from `lumberyard_install_dir\dev\Tools\LmbrSetup\Win\SetupAssistant.exe`\.

1.

   Using Setup Assistant, select the following capabilities for your O3DE configuration:
   + **Run your game project**
   + **Run the O3DE Editor and tools**
   + **Compile the game code**
   + **Compile the O3DE Editor and tools**
   + **Compile for Android Devices**

   After selecting these capabilities, select **Next** to continue with configuration in the Setup Assistant\.

1.  Provide the paths to the Android Studio installation, and the `ndk-build.cmd` and `adb.exe` Android tools commands\. These paths are used to determine the location of all of the Android build tools that O3DE uses\. If you performed a default install of Android Studio and its tools, Setup Assistant should have already detected the installation paths\.

    If you used another install location, or Setup Assistant couldn't find your Android install, provide the paths to the appropriate tools\. You can also select the **Browse** button to use Windows Explorer to navigate to the required files\.

   By default, the tools are located at the following paths\.
   + Android Studio installation - `C:\Program Files\Android\Android Studio`
   + `ndk-build.cmd` - `C:\Users\username\AppData\Local\Android\Sdk\ndk\version\ndk-build.cmd`
   + `adb.exe` - `C:\Users\username\AppData\Local\Android\Sdk\platform-tools\adb.exe`
![\[The Setup Assistant window displaying the paths for the required Android tools. Each path is surrounded by a colored box and is one of the default paths listed above.\]](/images/user-guide/platforms/android/setup-android-dev-2.png)
**Tip**
 If you can't find your Android SDK install in `C:\Users\username\AppData\Local\Android\Sdk`, the SDK location might have been changed in Android Studio\. To find out the location for your Android SDKs, follow these steps in Android Studio 4\.0\.x:
 Open the **Settings** pane in Android Studio, either by navigating to the **File** > **Settings** menu from an open project, or by selecting **Settings** from the startup window\.
 In the left navigation pane of the **Settings** window, select **Appearance & Behavior** > **System Settings** > **Android SDK**\.
Note the path displayed as the **Android SDK Location**\. Use this as the base path for locating the Android tools\.

![\[The Android Studio 4.0.1 "Setup" window. The left navigation has the nodes "Appearance & Behavior", "System Settings", and "Android SDK" expanded. Each is surrounded by a colored box. At the top of the main pane, the text field for "Android SDK Location" is surrounded by a colored box.\]](/images/user-guide/platforms/android/android-sdk-location.png)

   When each Android tool is marked with a green checkmark icon, you can close the Setup Assistant\.

1.  Check that the API level for the Android SDK you're using is correctly set in the O3DE build settings\.

   1. Open the file `lumberyard_install_dir\dev\_WAF_\android\android_settings.json` in a text editor\.

   1.  Change the value of `SDK_VERSION` to `android-apilevel`, and `NDK_VERSION` to `android-ndk-version`\. Use the API level of your development SDK for *apilevel*, and the API version of the NDK for *ndk\-version*\.

       For example, for Android Q \(API level 29\) and NDK API version 21, your configuration file will be similar to the following\.

      ```
      {
          "DEV_KEYSTORE_ALIAS" : "development_keystore",
          "DEV_KEYSTORE" : "_WAF_/android/dev.keystore",
          "DISTRO_KEYSTORE_ALIAS" : "distribution_keystore",
          "DISTRO_KEYSTORE" : "_WAF_/android/distro.keystore",
          "BUILD_TOOLS_VER" : "latest",
          "SDK_VERSION" : "android-29",
          "NDK_PLATFORM" : "android-21",
          "BUILD_ENVIRONMENT" : "Development"
      }
      ```

1. Configure the O3DE build system\.

   1. Open a Windows command prompt and navigate to `lumberyard_install_dir\dev`\.

   1. Run **lmbr\_waf configure**\.

    At this point, if you're missing any of the required packages or tools, or if O3DE needs additional configuration, `lmbr_waf configure` will report an error telling you what you need to install or edit to get your build working\. Use either [Android Studio SDK Manager](https://developer.android.com/studio/intro/update#sdk-manager) or [https://developer.android.com/studio/command-line/sdkmanager](https://developer.android.com/studio/command-line/sdkmanager) to install any missing packages or SDKs\.

**Important**
 At this point, **don't** rebuild your game or engine\. Wait until after you configure your project for Android, so that the O3DE tools will pick up those changes\.

## Add Android tools to `PATH` {#android-setting-up-path}

 When working with Android, it's useful to have access to the SDK tools directly from the command line\. If you're working with build infrastructure or on a development team for your project, we recommend that you add the Android build tools to your `PATH` environment variable\. To set an environment variable on Windows, follow these steps\.

1. Open the Windows **Control Panel** and select **System** > **Advanced system settings**\.

1. In the **System Properties** dialog box, select **Environment Variables**\.

1.  Under **User variables**, edit the **PATH** variable to add the **directories** containing the Android tools\. Select the **New** button to add a new value to `PATH`\. The values that you add are the directories containing the `adb.exe` and `ndk-build.cmd` files\.

## Build system setup {#android-setting-build-system}

 Now that you have Android support enabled in O3DE, you need to configure the build system to recognize your project and produce Android \(`es3`\) assets\.

1. Open the file `lumberyard_install_dir\dev\_WAF_\user_settings.options` in a text editor\.

1.  In the `[Game Projects]` section of the configuration file, change the value of `enabled_game_projects` to include the project that you want to build for Android\. This value setting is a comma\-separated list of the projects to generate Android targets for during configuration\. Either remove the existing values, or add your project name to the list\. For example, to enable `SamplesProject`, `MultiplayerSample`, and your project:

   ```
   [Game Projects]
   enabled_game_projects = SamplesProject, MultiplayerSample, your-project-name
   ```

1.  Save and close the file\.

1. Open the file `lumberyard_install_dir\dev\AssetProcessorPlatformConfig.ini` in a text editor\.

1. Remove the semicolon to uncomment `es3=enabled`\.

   ```
   [Platforms]
   ;pc=enabled
   es3=enabled
   ;ios=enabled
   ;osx_gl=enabled
   ```

1. Save and close the file\.

1. Start the Asset Processor to compile any existing assets for use on Android\.

   1. Check to see if the Asset Processor is running\. Open the Windows system tray and look for the Asset Processor icon\.
![\[The expanded Windows system tray. The icon for the O3DE Asset Processor is surrounded by a colored box.\]](/images/user-guide/platforms/android/ap-tray-icon.png)

   1. If the Asset Processor is running, double\-click the icon in the tray to bring up the Asset Processor window\.

   1. If the Asset Processor isn't running, launch it from `lumberyard_install_dir\dev\Bin64vc142\AssetProcessor.exe`\.

1.  Wait until the Asset Processor finishes building all of your assets for use with Android\. When assets are finished processing, the Asset Processor status line indicates that the Asset Processor is **Idle**\.

## Next steps {#android-setting-up-next-steps}

Now that you have your O3DE environment configured for Android, configure, build, and deploy your project to a device to test\.
+ [Configure O3DE projects for Android](/docs/user-guide/platforms/android/configure-project.md)
+ [Build and deploy your project for Android](/docs/user-guide/platforms/android/build-deploy.md)