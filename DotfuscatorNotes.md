This document contains notes we took while integrating Dotfuscator into 
the BugSweeper Xamarin app. You can step through this file's history in git
to see where we noted each point.

# Dotfuscator CLI path

We're using Dotfuscator Professional Edition 4.99.0:

`C:\Program Files (x86)\PreEmptive Solutions\Dotfuscator Professional Edition 4.99.0\dotfuscator.exe`

# Projects and configurations to protect

None. Everything we want to protect is protected!
  
# Protected projects and configurations

* `BugSweeper.Android` project
  * *Release* configuration
* `BugSweeper.iOS` project
  * *Release* configuration
  * *Ad-Hoc* configuration
  * *AppStore* configuration
* `BugSweeper.UWP` project
  * *Release* configuration

# Targets file path

`$(MSBuildExtensionsPath)\PreEmptive\Dotfuscator\4\PreEmptive.Dotfuscator.Xamarin.targets`

# Dotfuscator input directories

Android: `C:\code\BugSweeper\BugSweeper\BugSweeper.Android\obj\Release\DotfuscatorXamarin\dfin`  
iOS: `C:\code\BugSweeper\BugSweeper\BugSweeper.iOS\obj\iPhoneSimulator\Release\DotfuscatorXamarin\dfin`  
UWP: `C:\code\BugSweeper\BugSweeper\BugSweeper.UWP\obj\x64\Release\DotfuscatorXamarin\dfin`  
