---
linktTitle: Project Game Release Layout (Windows)
title: Creating a Project Game Release Layout for Windows
description: Learn how to create an Open 3D Engine (O3DE) project game release layout for Windows.
toc: true
weight: 200
---

This tutorial guides you through the process of creating an **Open 3D Engine (O3DE)** *project game release layout* for Windows computers. A project game release layout is a directory structure that contains the **Game Launcher** and the bundled assets needed to run the Game Launcher outside of the developer environment. You create a project game release layout when you build your project for release, known as a *release build*.

{{< important >}}
In order to create a project game release layout, you must have O3DE set up from source, not just the installer. Refer to [Setting up O3DE from GitHub](/docs/welcome-guide/setup/setup-from-github/). 
{{< /important >}}

A release build requires *bundled content*, which includes cached product assets stored in package (`.pak`) files. Cached product assets are located in the project's `Cache\pc` directory. The Game Launcher loads the bundled content that makes up a project, such as its levels, objects, environments, and gameplay logic.

The instructions here guide you through the following steps:

1. Set the starting level.

1. Process your project's assets.

1. Create a project game release layout.

1. (Optional) Bundle your project's assets.

1. Run your project's Game Launcher from the project game release layout.

1. Distribute your build.


## Prerequisites

The following instructions assume that you have:

- Set up O3DE on your computer as either a *source engine* or *pre-built SDK engine*. For help, refer to [Building for Windows](/docs/welcome-guide/setup/setup-from-github/building-windows). If you only set up O3DE using the [Windows installer](/docs/welcome-guide/setup/installing-windows), you cannot create a project game release layout.

- Created an O3DE project that contains at least one level. To build your project for release, you might need to resolve any errors in your project.

- Generated a Visual Studio project for either of the following:
  
  - Your project -- If you're using a source engine, you must generate a Visual Studio project for your project. For help, refer to [Create a Visual Studio project](/docs/welcome-guide/create/creating-projects-using-cli/creating-windows#create-a-visual-studio-project).

  - Your engine -- If you're using a pre-built SDK engine, you must generate a Visual Studio project for your engine. For help, refer to the **Pre-built SDK engine** steps in [Building for Windows](/docs/welcome-guide/setup/setup-from-github/building-windows/).

- Registered your project to your engine.


## Tutorial specifics

This tutorial uses the following directories in the examples.

- Project name and directory: `C:\MyProject`
- Third-party directory: `C:\o3de-packages`
- Source engine: `C:\o3de`
- Pre-built SDK engine: `C:\o3de-install`


## Set the starting level
{{< note >}}
This step is optional if you set the starting level in your project's code base. A more common practice in development is to set the starting level in the project's code base via a level manager or similar system.
{{< /note >}}

**Prerequisite**: You must have created at least one level in your project. Check out the [Create a level](/docs/learning-guide/tutorials/environments/create-a-level/) tutorial to start. 

Before you create a project game release layout, your project must specify a starting level to inform the Game Launcher which level it should load first. You can specify the starting level in your project's settings registry (`.setreg`) or in your project's code base via a level manager or similar system that your team develops.

For this example, set your project's starting level in the settings registry by creating an `autoexec.game.setreg` file in the `C:\MyProject\Registry` directory. Then, add the following lines.

```JSON
{
    "O3DE": {
        "Autoexec": {
            "ConsoleCommands": {
                "LoadLevel": "mainmenu"
            }
        }
    }
}
```

This configuration specifies the starting level by setting `Loadlevel` to the name of the level. For this example, the level asset is named `mainmenu`. You don't need to specify the level asset's file extension, such as `main.prefab` or `main.spawnable`. You can find your project's levels in the `C:\MyProject\Levels` directory.


## Process assets

Use **Asset Processor** or the **Asset Processor Batch** program to process source assets into product assets, which the Game Launcher consumes at runtime. Asset Processor provides a graphical user interface (GUI), while Asset Processor Batch provides a command line interface (CLI).

### Methods to process assets

To process your project's assets, do one of the following:

- **Run Asset Processor.** Do this if you already built Asset Processor and prefer to use a GUI.

    1. Run `AssetProcessor.exe` from the `<engine>/build/windows_vs2019/bin/profile` directory.

        {{< image-width "/images/user-guide/packaging/windows-release-build/asset-processor.png" "700" "An image of O3DE Asset Processor." >}}

    1. Wait for all jobs to complete. You can view the status of each asset in the **Asset Status** pane and the details of each asset in the **Event Log Details** pane.

- **Run Asset Processor Batch.** Do this if you already built Asset Processor and prefer to use a CLI.

    1. Run `AssetProcessorBatch.exe` from the `<engine>\build\windows_vs2019\bin\profile` directory.

        {{< image-width "/images/user-guide/packaging/windows-release-build/asset-processor-batch.png" "700" "An image of O3DE Asset Processor Batch." >}}

    1. Wait for all jobs to complete. You can view the output in the `AP_Batch.log` file in the `<project>\user\log` directory.

- **Build your project's Asset Processor tools.** Do this if you have not yet built Asset Processor and Asset Processor Batch.

    1. Use **CMake** to invoke Visual Studio to build the Asset Processor tools and their dependencies. Specify the `MyProject.Assets` target.

        ```cmd
        cmake --build build\windows_vs2019 --target MyProject.Assets --config profile -- -m
        ```

        First, this command builds the Asset Processor tools. Then, it runs Asset Processor Batch and processes the assets. You don't need to rerun Asset Processor or Asset Processor Batch unless you make changes in your project.

    1. Wait for all jobs to complete. You can view the output in the `AP_Batch.log` file in the `<project>\user\log` directory.

### Verify assets are successfully processed

Verify that Asset Processor successfully processed your assets by checking their status. Asset Processor can process your assets with or without warning or error, or they can fail to process. Warnings and errors indicate that your asset may need your attention, but they don't impede the ability to create a project game release layout. On the other hand, it's critical that you resolve any assets that were not processed due to failure.

For help with debugging failures, refer to [Debugging](#debugging).


## Create a project game release layout

Now that you've prepared your project, you can create a project game release layout. You can do this in different ways, depending on how you set up your engine and whether you want to create a non-monolithic or monolithic release build.

When you set up O3DE, you built your engine as either a *source engine* or a *pre-built SDK engine*. You can use either build type to create a project game release layout. For more information on build types, refer to [Build the engine](/docs/welcome-guide/setup/setup-from-github/#build-the-engine).

Additionally, you can create a project game release layout that's either *non-monolithic* or *monolithic*. A non-monolithic build uses Gems as dynamically linked libraries (`.dll`), whereas a monolithic build uses Gems as statically linked libraries (`.lib`). Using Gems as `.lib` files in a monolithic build strips out unused pieces of code and removes redundant symbols across them. As a result, the total space on disk for a monolithic build is smaller than for a non-monolithic build.

{{< note >}}
The final output of a monolithic build might contain some `.dll` files. This can occur when third-party libraries provide only `.dll` files.
{{< /note >}}

In this step, you use CMake to build either a non-monolithic or a monolithic project for release. This handles several tasks:

1. Creates an *install directory* that contains the project game release layout. By default, the install directory is located in your project's directory (for example, `C:\MyProject\install`). Alternatively, you can set the install directory to another location by setting `CMAKE_INSTALL_PREFIX` in the `CMakeCache.txt` file in the `<project>\build\<build>` directory (for example, `C:\MyProject\build\windows_vs2019\CMakeCache.txt`).

1. Bundles your game content by packaging all of the product assets in the `<project>\Cache\pc` directory into an `engine.pak` file.

1. Builds the Game Launcher and other supporting files in the project game release layout.

{{< tabs name="release-build-instructions" >}}
{{% tab name="Source engine" %}}

### Source engine

With a source engine, you must use a project-centric configuration to create a project game release layout. In a project-centric configuration, the CMake source directory is in the project build directory. So O3DE tools (for example, **O3DE Editor** and Asset Processor) are located in your `<project>\build` directory.

To create a non-monolithic or monolithic project game release layout using a source engine, complete the following steps.

#### Option 1: Non-monolithic

A source engine supports non-monolithic projects by default. As detailed in [Prerequisites](#prerequisites), you should have already created a Visual Studio project for your project and registered your project to your engine.

To build your non-monolithic project for release, use CMake to invoke Visual Studio builder. Specify the `INSTALL` target and `release` configuration.

```cmd
cd C:\MyProject
cmake --build build\windows_vs2019 --target INSTALL --config release 
```

You can specify a particular non-monolithic build by appending the option `-DLY_MONOLITHIC_GAME=0`. This command generates O3DE tools (such as Editor, Asset Processor, and Game Launcher) and dependent `.dll` files. It also bundles your project's product assets that are located in `<project>\Cache\pc` into an `engine.pak` file.

The result is a project game release layout in the install directory that's located at `<install>\bin\Windows\release\Default`. In your project game release layout, your `engine.pak` file is located in `Cache\pc`.


#### Option 2: Monolithic

1. Use CMake to create a Visual Studio project for a monolithic project. Specify a new CMake build directory (`build\windows_mono`) in your project directory to separate it from your non-monolithic build directory. Specify a monolithic build by enabling the `-D` option to `LY_MONOLITHIC_GAME`.

    ```cmd
    cd C:\MyProject
    cmake -B build\windows_mono -S . -G "Visual Studio 16" -DLY_3RDPARTY_PATH=C:\o3de-packages -DLY_MONOLITHIC_GAME=1
    ```

1. To build your monolithic project for release, use CMake to invoke Visual Studio builder. Specify the `INSTALL` target and `release` configuration.

    ```cmd
    cd C:\MyProject
    cmake --build build\windows_mono --target INSTALL --config release
    ```

    This command generates a Game Launcher and dependent `.lib` files. It also bundles your project's product assets that are located in `<project>\Cache\pc` into an `engine.pak` file.

The result is a project game release layout in the install directory that's located at `<install>\bin\Windows\release\Monolithic`. In your project game release layout, your `engine.pak` file is located in `Cache\pc`.

{{% /tab %}}
{{% tab name="Pre-built SDK engine" %}}

### Pre-built SDK engine

With a pre-built SDK engine, to create a project game release layout, you must append non-monolithic or monolithic artifacts.

Verify that your project is registered to your pre-built SDK engine by checking your project's `project.json` file. To register your project, use O3DE CLI from your pre-built SDK engine.

```cmd
cd C:\o3de-install
scripts\o3de.bat register --project-path C:\o3de-projects\MyProject
```

#### Option 1: Non-monolithic

A pre-built SDK engine supports non-monolithic projects by default. As detailed in [Prerequisites](#prerequisites), you should have already created a Visual Studio project for the engine and registered the engine.

1. Reconfigure your source engine with the `-D` option, `LY_PROJECTS`, pointing to your project's path.

    ```cmd
    cd C:\o3de
    cmake -B build/windows_vs2019 -G "Visual Studio 16" -DLY_3RDPARTY_PATH=C:\o3de-packages -DLY_VERSION_ENGINE_NAME=o3de-install -DCMAKE_INSTALL_PREFIX=C:\o3de-install -DLY_PROJECTS=C:\o3de-projects\MyProject
    ```

1. In your source engine, use CMake to invoke Visual Studio to append non-monolithic release artifacts to the pre-built SDK layout.

    ```cmd
    cd C:\o3de
    cmake --build build\windows_vs2019 --target INSTALL --config release
    ```

    You can specify a particular non-monolithic build by appending the option `-DLY_MONOLITHIC_GAME=0`. This command generates O3DE tools (such as Editor, Asset Processor, and Game Launcher) and dependent `.dll` files. It also bundles your project's product assets that are located in `<project>\Cache\pc` into an `engine.pak` file.

The result is a project game release layout in the install directory that's located at `<install>\bin\Windows\release\Default`. In your project game release layout, your `engine.pak` file is located in `Cache\pc`.



#### Option 2: Monolithic

1. Use CMake to create a Visual Studio project for an engine that supports monolithic projects. Specify a new CMake build directory (`build\windows_mono`) in your pre-built SDK engine directory, separate from your non-monolithic build directory. Specify a monolithic build by enabling the `-D` option, `LY_MONOLITHIC_GAME`.

    ```cmd
    cd C:\o3de
    cmake -B build\windows_mono -S . -DCMAKE_INSTALL_PREFIX=C:\o3de-install -DLY_VERSION_ENGINE_NAME=o3de-install -DLY_MONOLITHIC_GAME=1 -DLY_PROJECTS=C:\o3de-projects\MyProject
    ```
    - Include `-D` option, `LY_PROJECTS`, to point to your project's path.

2. In your source engine, use CMake to invoke Visual Studio to append non-monolithic release artifacts to the pre-built SDK layout.

    ```cmd
    cd C:\o3de
    cmake --build build\windows_mono --target INSTALL --config release
    ``` 

    This command generates a Game Launcher and dependent `.lib` files. It also bundles your project's product assets that are located in `<project>\Cache\pc` into an `engine.pak` file.

The result is a project game release layout in the install directory that's located at `<install>\bin\Windows\release\Monolithic`. In your project game release layout, your `engine.pak` file is located in `Cache\pc`.

{{% /tab %}}
{{< /tabs >}}

## (Optional) Bundle content

**Asset Bundler** and the **Asset Bundler Batch** program are tools that let you create and optimize your bundled content. When you created a project game release layout earlier, you bundled all of your assets into an `engine.pak` file, which contains all of your project's product assets. If your project contains many unused assets, the `engine.pak` file might be unoptimized. To optimize your bundled content, use Asset Bundler and configure how you want to bundle your assets.

Follow the instructions in [Bundling Assets for a Project Game Release Layout](asset-bundler/bundle-assets-for-release). There, you will create two bundles: `game.pak`, for game assets, and `engine.pak`, for engine assets. Then, you will add the bundles to your project game release layout. 

You may notice that both your bundled assets are smaller than the default `engine.pak` that was created automatically when you created a project game release layout earlier. 


## Run the Game Launcher

Now you're ready to run your project's Game Launcher.

Run `GameLauncher.exe` from your project game release layout, which is located in the `<install>\bin\Windows\release\<build>` directory. For example, the path could be:

- `C:\MyProject\install\bin\Windows\release\Default` -- For non-monolithic projects.

- `C:\MyProject\install\bin\Windows\release\Monolithic` -- For monolithic projects.


## Distribute your build

To distribute your build to other Windows computers:

1. Create a `.zip` file of the `<install>\bin\Windows\release\<build>` folder. The contents of the `.zip` file should look similar to this:

        Monolithic
        |   DevTestProject.GameLauncher.exe
        |   PhysXDevice64.dll
        |   PhysXGpu_64.dll
        |   
        \---Cache
            \---pc
            engine.pak
                
1. Distribute the `.zip` file using your preferred method, such as a file storage service. 

Now others can download and unpack the `.zip` file and run the Game Launcher on their Windows computers.

{{< note >}}
To run the Game Launcher on the other Windows computers, they must have the [Microsoft Visual C++ (MSVC) Redistributable](https://docs.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist?view=msvc-160) installed.
{{< /note >}}
