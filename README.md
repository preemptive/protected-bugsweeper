This is an example repository that demonstrates setting up the Dotfuscator-Xamarin integration
described here: <https://www.preemptive.com/obfuscating-xamarin-with-dotfuscator>.

>**Note**: If you are using Dotfuscator Community Edition or Dotfuscator Professional 4.33 and earlier, use the [original-dotfuscator-integration](https://github.com/preemptive/protected-bugsweeper/tree/original-dotfuscator-integration) tag.

You can step through this repository's history to see how we set up Dotfuscator's protection
in an existing Xamarin app. Notes that we took along the way can be found in [DotfuscatorNotes.md](DotfuscatorNotes.md).

Original README from the Xamarin app follows.
You can download the original app from <https://developer.xamarin.com/samples/xamarin-forms/BugSweeper/>.

----------------------------------

BugSweeper
======

This is a familiar game with a new twist. Ten bugs are hidden in a 9-by-9 grid of tiles.
To win, you must find and flag all ten bugs.

**If you open the solution in Xamarin Studio, it will not be able to load the Windows Phone project;
and if you open the solution in Xamarin Studio under Windows, it will not be able to load the iOS project either.**

Begin playing by double tapping any tile. That first double tap is always safe.
Thereafter, numbers indicate the number of surrounding tiles with bugs. 
If you know that a tile has a bug, flag it (or unflag it) with a single tap.
Avoid double-tapping a tile with a bug!

**Original author**: Charles Petzold