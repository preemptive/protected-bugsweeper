This is an example repository that demonstrates integrating [Dotfuscator](https://www.preemptive.com/products/dotfuscator/overview) protection into a Xamarin app.
The process on this branch should be followed if you are using Dotfuscator Professional 4.37.0 or later.
If you are using an earlier version of Dotfuscator Professional, or if you are using Dotfuscator Community, please see the **protected-community** branch.

This branch has the finished result of protecting BugSweeper with Dotfuscator.
The instructions below explain how to take the unprotected solution (i.e., as of the **master** branch) and apply Dotfuscator's protection.
You can also review the commit history of this branch to see how these steps were followed to produce this protected branch.

# Protecting BugSweeper

The following process (steps 1-4) need to be followed for **each** output project (BugSweeper.Android, BugSweeper.UWP, BugSweeper.iOS) in the solution in order for the app to be protected on each platform.

## 1. Integrate into Your Visual Studio Project

First you need to configure Dotfuscator to run during the build process.
See the [Integrate Dotfuscator](https://www.preemptive.com/dotfuscator/pro/userguide/en/getting_started_protect.html#integrate-xamarin) instructions on what to paste into the output project to integrate Dotfuscator.

After Dotfuscator is integrated, you can build the project in a protected configuration (e.g., Release), and a Dotfuscator config (`DotfuscatorConfig.xml`) file will be generated for you. 
At this point you should commit the Dotfuscator config file to version control.

As part of the build, Dotfuscator will protect the output assembly of the project (e.g., `BugSweeper.Android.dll`) as well as assemblies the project obtains with project references (e.g., `BugSweeper.dll` and `BugSweeperTile.dll`) in the project's output directory (e.g., `bin\Release`).

For Android and iOS, you should be able to run the BugSweeper app with the default protection applied; however, you should notice that when you flag a bug in the app the flagged count displayed does not update.
This means that a renaming exclusion is needed. 
Similarly, if you try to run BugSweeper on UWP at this point you will notice it won't run, because renaming exclusions are needed.

## 2. Configure Renaming Exclusions

Dotfuscator renames code elements (e.g., classes, methods, etc.) in the assemblies it protects. 
Occasionally, you may need to exclude certain code elements from renaming to ensure proper application behavior.

To configure renaming exclusions, launch the [Dotfuscator Professional Config Editor](https://www.preemptive.com/dotfuscator/pro/userguide/en/interfaces_config_editor_overview.html), open the `DotfuscatorConfig.xml` config file, and set renaming exclusions on the [Rename tab's Exclude sub-tab](https://www.preemptive.com/dotfuscator/pro/userguide/en/interfaces_config_editor_renaming.html#exclude-tab).
For this sample, the following exclusions are necessary:
* Android and iOS: 
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
This feature is helpful when getting set up, but once the file exists (and is tracked by version control) you should disable this feature because it can mask a [certain kind of build error](https://www.preemptive.com/dotfuscator/pro/userguide/en/interfaces_msbuild.html#targets-config-nogen).

## 4. Enhance Protection

Dotfuscator offers default protection settings when you first integrate it into a project.
However, Dotfuscator protection can be made much stronger than these defaults.
Follow along with the [Enhance Protection](https://www.preemptive.com/dotfuscator/pro/userguide/en/getting_started_enhance.html) section of the Dotfuscator Professional User Guide to strengthen the protection applied to BugSweeper.

[Root Checks](https://www.preemptive.com/dotfuscator/pro/userguide/en/protection_checks_root.html) can be added to BugSweeper.Android to prevent the app from running on rooted devices.
The Root Check in this sample is placed on the `OnSingleTap` method and configured to make the app exit when that method is called on rooted devices.
After configuring the root check, you can test BugSweeper on a rooted device or emulator and see that when you try to flag a bug, the app will now exit.
See our [Protected-TodoAzureAuth](https://github.com/preemptive/Protected-TodoAzureAuth) sample for more detail on adding Root Checks to Xamarin apps.

[Tamper Checks](https://www.preemptive.com/dotfuscator/pro/userguide/en/protection_checks_tamper.html) can be added to BugSweeper.Android to prevent running a modified app.
The Tamper Check in this sample is placed on `NewGameInitialize` method and is configured to make the app exit when that method is called in an apk that has been tampered with.
The Tamper Check depends on the apk being signed, so app signing has been setup with a sample keystore included alongside the Android project.
After configuring the Tamper Check, you can test Bugsweeper by re-signing with another certificate.
This simulates an attacker making code changes and resigning with their own certificate.
You will then see upon starting, the app will exit.

The Renaming obfuscation of the BugSweeper can be made stronger by turning off library mode.
In the *Input* tab of the Dotfuscator Config Editor tab expand the `BugSweeper.dll` and `BugSweeperTile.dll` nodes and uncheck *Library*.
If you save the config, build in Visual Studio, and run the app, you will see that another renaming exclusion is needed after disabling library mode on these assemblies.
Exclude the type `BugSweeper.BugSweeperPage` and the app should now run correctly the next time you build.
Renaming obfuscation can further be improved by [Enabling Enhanced Overload Induction](https://www.preemptive.com/dotfuscator/pro/userguide/en/getting_started_enhance.html#renaming-enhancedoi).

After enhancing the Renaming obfuscation, in the *Settings Tab* in the *Advanced* options section turn the [Suppress Ildasm](https://www.preemptive.com/dotfuscator/pro/userguide/en/getting_started_enhance.html#controlflow-suppressildasm) feature to prevent newer versions of Visual Studio from [decompiling](https://docs.microsoft.com/en-us/visualstudio/ide/go-to-and-peek-definition#view-decompiled-source-definitions-instead-of-metadata-c) assemblies back into C# code and stop the official [.NET disassembler](https://www.preemptive.com/dotfuscator/pro/userguide/en/protection_reverse_engineering.html#ildasm). 
Note that this setting doesn't affect third party tools.

To enhance the protection of the app further turn on [String Encryption](https://www.preemptive.com/dotfuscator/pro/userguide/en/protection_obfuscation_string_encryption.html) for all of the assemblies. 
Enable String Encryption on the *Settings* tab's *Options* screen, under *Feature*, by setting *Disable String Encryption* to **No**.
Then in the *String Encryption Tab* check the box next to each Input Assembly in the *Include* section.

Once you have finished enhancing the protection you can move on and repeat steps 1-4 for the remaining output projects.

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