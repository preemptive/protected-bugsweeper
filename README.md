This is an example repository that demonstrates integrating [Dotfuscator](https://www.preemptive.com/products/dotfuscator/overview) protection into a Xamarin app.

This branch contains the unprotected version of the BugSweeper Sample project.
The process for integrating Dotfuscator protection into this project can be found on the other branches:
* Follow the **protected-professional** branch if you are using Dotfuscator Professional 4.37.0 or later. Additional help can be found in the [Protect Your App](https://www.preemptive.com/dotfuscator/pro/userguide/en/getting_started_protect.html#integrate-xamarin) section of our Dotfuscator Professional User Guide.
* Follow the **protected-community** branch if you are using either Dotfuscator Community or a version of Dotfuscator Professional earlier than 4.37.0. Additional help can be found in the [Xamarin Getting Started](https://www.preemptive.com/dotfuscator/ce/docs/help/getting_started_xamarin.html) section in our Dotfuscator Community User Guide.

You can download the original app from <https://developer.xamarin.com/samples/xamarin-forms/BugSweeper/>.
Please note however, we split out the Tile.cs class into a separate .NET Standard class library (BugSweeperTile.dll) to demonstrate more of Dotfuscator's protection.

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