This is an example repository that demonstrates integrating [Dotfuscator](https://www.preemptive.com/products/dotfuscator/overview) protection into a Xamarin app.
The process on this branch should be followed if you are using a version of Dotfuscator Professional earlier than 4.37.0 or if you are using Dotfuscator Community.
If you are using Dotfuscator Professional 4.37.0 or later, you should follow the **protected-professional** branch.

This branch has the finished result of protecting BugSweeper with Dotfuscator.
The instructions below explain how to take the unprotected solution (i.e., as of the **master** branch) and apply Dotfuscator's protection.
You can also review the commit history of this branch to see how these steps were followed to produce this protected branch.

# Protecting BugSweeper

First you'll need to download the a custom set of MSBuild targets and tasks, for Dotfuscator: [Download](https://www.preemptive.com/dotfuscator/ce/downloads/PreEmptive.Dotfuscator.Xamarin.zip).

We recommend saving the extracted contents in your app's solution directory and adding it to your local source control, as it will be required for building your projects.

The following process (steps 1-4) need to be followed for **each** output project (BugSweeper.Android, BugSweeper.UWP, BugSweeper.iOS) in the solution in order for the app to be protected on each platform.

## 1. Integrate into Your Visual Studio Project

First you need to configure Dotfuscator to run during the build process.

Determine the relative path from the project directory to the PreEmptive.Dotfuscator.Xamarin.targets file you saved earlier.
This will used in the Import statement below.
Also determine the [location of the Dotfuscator CLI](https://www.preemptive.com/dotfuscator/ce/docs/help/getting_started_cli.html#locating).
This will be the value of the DotfuscatorXamarinCliPath.

Copy the new elements shown below into the appropriate positions in your output project file (e.g., `BugSweeper.Android.csproj`) immediately before the closing `</Project>` tag.
Note that the order of the elements is important and the you may need to change the paths of `DotfuscatorXamarinCliPath` and the imported project to fit to the values you determined above.

```
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="4.0" DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">

  <!-- ...existing tags... -->

  <!-- Track the Dotfuscator config file in the project -->
  <ItemGroup>
    <None Include="DotfuscatorConfig.xml" />
  </ItemGroup>

  <!-- existing import of platform specific Xamarin targets />

  <!-- Set build properties for Dotfuscator -->
  <PropertyGroup>

    <!-- Config file name-->
    <DotfuscatorXamarinConfigFileName>DotfuscatorConfig.xml</DotfuscatorXamarinConfigFileName>

    <!-- Path to the Dotfuscator CLI -->
    <DotfuscatorXamarinCliPath>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\Common7\IDE\Extensions\PreEmptiveSolutions\DotfuscatorCE\dotfuscatorCLI.exe</DotfuscatorXamarinCliPath>

    <!-- Generate a default Dotfuscator config file (DotfuscatorConfig.xml) -->
    <!-- TODO: Set this to false after the file is generated by the first local build -->
    <DotfuscatorXamarinGenerateNewConfigFile>true</DotfuscatorXamarinGenerateNewConfigFile>

    <!-- Enable Dotfuscator for Release -->
    <DotfuscatorXamarinEnabled Condition="'$(Configuration)' == 'Release'">true</DotfuscatorXamarinEnabled>

    <!-- Enable Dotfuscator for Ad-Hoc (only needed for iOS) -->
    <DotfuscatorXamarinEnabled Condition="'$(Configuration)' == 'Ad-Hoc'">true</DotfuscatorXamarinEnabled>

    <!-- Enable Dotfuscator for AppStore (only needed for iOS) -->
    <DotfuscatorXamarinEnabled Condition="'$(Configuration)' == 'AppStore'">true</DotfuscatorXamarinEnabled>

  </PropertyGroup>
  <!-- Relative path to the custom set of MSBuild targets and tasks saved earlier -->
  <Import Project="../../PreEmptive.Dotfuscator.Xamarin/PreEmptive.Dotfuscator.Xamarin.targets"/>
</Project>
```

For more help integrating Dotfuscator see the [Xamarin getting Started](https://www.preemptive.com/dotfuscator/ce/docs/help/getting_started_xamarin.html) page. 

After Dotfuscator is integrated, you can build the project in a protected configuration (e.g., Release), and a Dotfuscator config (`DotfuscatorConfig.xml`) file will be generated for you. 
At this point you should commit the Dotfuscator config file to version control.

As part of the build, Dotfuscator will protect the output assembly of the project (e.g., `BugSweeper.Android.dll`) as well as assemblies the project obtains with project references (e.g., `BugSweeper.dll` and `BugSweeperTile.dll`) in the project's output directory (e.g., `bin\Release`).

For iOS, you should be able to run the BugSweeper app with the default protection applied; however, you should notice that when you flag a bug in the app the flagged count displayed does not update.
This means that a renaming exclusion is needed. 
Similarly, if you try to run BugSweeper on Android or UWP at this point you will notice it won't run, because renaming exclusions are needed.

## 2. Configure Renaming Exclusions

Dotfuscator renames code elements (e.g., classes, methods, etc.) in the assemblies it protects.
Occasionally, you may need to exclude certain code elements from renaming to ensure proper application behavior.

You can configure renaming exclusions in the appropriate user interface for your edition of Dotfuscator:
* For Dotfuscator Professional, launch the [Dotfuscator Professional Config Editor](https://www.preemptive.com/dotfuscator/pro/userguide/en/interfaces_config_editor_overview.html), open the `DotfuscatorConfig.xml` config file, and set renaming exclusions on the [Rename tab's Exclude sub-tab](https://www.preemptive.com/dotfuscator/pro/userguide/en/interfaces_config_editor_renaming.html#exclude-tab).
* For Dotfuscator Community (included in Visual Studio), launch the [Dotfuscator Community user interface](https://www.preemptive.com/dotfuscator/ce/docs/help/getting_started_gui.html), open the `DotfuscatorConfig.xml` config file, and set renaming exclusions on the [Renaming tab's Exclusions tab](https://www.preemptive.com/dotfuscator/ce/docs/help/gui_renaming.html#exclude).

For this sample, the following exclusions are necessary:
* Android: 
	* `FlaggedTileCount` property
  * `BugCount` property
  * `BugSweeperPage` type
  * `board` field
* iOS: 
  * `FlaggedTileCount` property
* UWP: 
	* `BugSweeper.Board` type
	* `board` field
	* `InitializeComponent` method
	* `OnBoardContentViewSizeChanged` method
	* `OnMainContentViewSizeChanged` method
	* `OnplayAgainButtonClicked` method

For general details on how to discover renaming exclusions necessary for a project, see [Identifying Renaming Exclusions](https://www.preemptive.com/dotfuscator/pro/userguide/en/protection_exclusions.html) in the Dotfuscator Professional User Guide.

## 3. Disable Config File Generation

Next, open the project file and change the value of `DotfuscatorGenerateConfigFileIfMissing` from `true` to `false` .
This feature is helpful when getting set up, but once the file exists (and is tracked by version control) you should disable this feature because it can mask a build error when the Dotfuscator config file is missing because a new one is automatically generated when this value is set to true.
For more information see the [Disable Config File Generation](https://www.preemptive.com/dotfuscator/ce/docs/help/getting_started_xamarin.html#team-disable-generation) section of the Dotfuscator Community docs.

## 4. Enhance Protection

Dotfuscator offers default protection settings when you first integrate it into a project.
However, Dotfuscator protection can be made much stronger than these defaults.

[Root Checks](https://www.preemptive.com/dotfuscator/ce/docs/help/checks_root.html) can be added to BugSweeper.Android to prevent the app from running on rooted devices.
The Root Check in this sample is placed on the `OnSingleTap` method and configured to make the app exit when that method is called on rooted devices.
After configuring the root check, you can test BugSweeper on a rooted device or emulator and see that when you try to flag a bug, the app will now exit.
See our [Protected-TodoAzureAuth](https://github.com/preemptive/Protected-TodoAzureAuth) sample for more detail on adding Root Checks to Xamarin apps.

The Renaming obfuscation of the BugSweeper can be made stronger by turning off library mode.
In Dotfuscator, on the *Inputs* tab, uncheck the library mode property for `BugSweeper.dll` and `BugSweeperTile.dll`.
If you save the config, build in Visual Studio, and run the app, you will see that another renaming exclusion is needed after disabling library mode on these assemblies for the iOS and UWP platforms.
Exclude the type `BugSweeper.BugSweeperPage` and the app should now run correctly the next time you build.

If you are using Dotfuscator Professional, see the [Enhance Protection](https://www.preemptive.com/dotfuscator/pro/userguide/en/getting_started_enhance.html) section of the Dotfuscator Professional User Guide for ways to further strengthen the protection applied to BugSweeper.

Once you have finished enhancing the protection you can move on and repeat steps 1-4 for the remaining output projects.

### Note

For UWP, you may need to tell Dotfuscator where to find the assembly reference `Windows.Foundation.UniversalApiContract` if the location differs from the configured location. 
You can change this under [Settings > Assembly Load Paths](https://www.preemptive.com/dotfuscator/ce/docs/help/gui_settings.html#pctoc-assembly-load-paths-tab).

Original README from the Xamarin app follows.

----------------------------------


BugSweeper
======

This is a familiar game with a new twist. Ten bugs are hidden in a 9-by-9 grid of tiles.
To win, you must find and flag all ten bugs.

Begin playing by double tapping any tile. That first double tap is always safe.
Thereafter, numbers indicate the number of surrounding tiles with bugs. 
If you know that a tile has a bug, flag it (or unflag it) with a single tap.
Avoid double-tapping a tile with a bug!

**Original author**: Charles Petzold
