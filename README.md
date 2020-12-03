# CHECKOUT AND BUILD CHROMIUM WALKTHROUGH | [![Twitter URL](https://img.shields.io/twitter/url/https/twitter.com/_DiegoMoura.svg?style=social&label=Follow%20%40_DiegoMoura)](https://twitter.com/_DiegoMoura)
[Official Checking out and building Chromium for Mac](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md#System-requirements).
## My experience and tips

The process of Checkout and Build for Chromium is straightforward, but I had to take several steps to complete the process due to my machine's configuration. This walkthrough **is not in any way a substitute for the [oficial documentation](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md)**, my intention is only to share my experience in the hope of helping other devs facing similar problems.
#### *My Setup:*
* MacBook Air (13-inch, 2017)
* 1.8 GHz Dual-Core Intel Core i5
* 8 GB 1600 MHz DDR3
* Storage: The more, the better. I had 50GB available on my Macbook, and I'm using an external HD with 3T for the codebase.
* Terminal: ITerm2* [official page](https://iterm2.com/)
* Terminal Framework: Oh-my-zhs* [official page](https://ohmyz.sh/) 
> \* 100% Optional you can do everything with the out-of-the-box MacOS Terminal.
### Step 1
#### *System Requirements:*
* A 64-bit Mac running 10.14+.
* Xcode 11.2+
* The OS X 10.15.1 SDK. Run

**Run the command below to make sure you have the SDK.**
```
    $ ls `xcode-select -p`/Platforms/MacOSX.platform/Developer/SDKs
```
> Note: If you like myself have **CommandLineTools** installed, the command return "not found," and you will have to download Xcode anyway. So go ahead and do it, the file requires approximately 42GB, and it takes some time to continue while Xcode download. More about possible bugs that arise from this on step number 4.
### Step 2
#### *Install depot_tools:*
Run the command below to clone depot_tools.
```
    $ git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
```
> I recommend cloning it to you $HOME folder (eg. ~./zshrc)

*Add depot_tools to the end of our PATH:*
Run the command below and change `/path/to/` with the PATH you used to clone depot_tools
```
    $ export PATH="$PATH:/path/to/depot_tools"
```
> Note: you must use the absolute path, or Python will not find infra tools. Here is an example of how my absolute path looks like `/Users/diegoosvaldodemoura/depot_tools`.

> tip: Right-click the depot_tools folder (command + i) and copy all the content of the field **where**. That's your PATH to the folder. By pasting it into the terminal in the correct spot of the command, the shell will take care of making the corrections (like removing the MacintoshHD from the PATH.)
### Step 3
#### *Get the code*
Run to ensure that unicode filenames aren't mangled by HFS:
```
$ git config --global core.precomposeUnicode true
```
Make a directory for the source code (you can call this whatever you like and put it wherever you like, as long as the full path has no spaces)
```
$ mkdir chromium && cd chromium
```
Run
```
    $ fetch chromium
```
> Note: If the command doesn't work, there is probably something wrong with your PATH. The fetch tool is part of the depot_tools and can only run if your PATH has the directory at the end. Visit the official page for more information [Official Checking out and building Chromium for Mac](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md#System-requirements).

> note 2: The first time I did the checkout process, it took around 2hours and 30 minutes (8-10:30 pm). The second time I did the same process starting at 8 am, and it took 6 hours. Be aware of the time of the day you chose to work on this, as it seems that the later at night where traffic is lower, the speed is much better.

Once the process is finished, a  new folder src has been created inside the folder you used to fetch the code, and the following steps assume you are now running the commands from inside that folder. So!
```
    $ cd src
```
### Step 4
#### *Setting up the Build:*
Here is where some issues from having the CommandLineTools installed and have installed Xcode after may appear.
Run
```
    $ autoninja -C out/Default chrome
```
> note: that **Default** may be changed for whatever you like, but not the **out/** path. Visit the official page for more information [Official Checking out and building Chromium for Mac](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md#System-requirements).
#### *Error handling:*
If you get the error:
```
    xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance
```
This means that you previously installed CommandLineTools is blocking the PATH, and the depot_tools can't communicate with the new *xcode-select* from your newly installed Xcode app. 
Fixing it:
1) Ensure Xcode app is in the `/Applications` directory (NOT `/Users/{user}/Applications`)
2) Run:
```
    sudo xcode-select -s /Applications/Xcode.app/Contents/Developer`
```
3) Make sure you have the correct version of Xcode in the PATH (eg. `Xcode.app`, `Xcode-beta.app`)
4) All done, rerun the build command.
> tip: Xcode crashed when I ran the build command, returning an error. Still, the built initialization was successful. So if that happens to you, make sure to check the command line for the output `Done. Made xxxx targets from xxxx files in xxxx.ms` before you get frustrated and close the terminal on a built that is happening despite Xcode errors.

> tip 2: Official guide on StackOverflow on how to correct the xcode-select PATH [here](https://stackoverflow.com/questions/17980759/xcode-select-active-developer-directory-error/17980786#17980786).

#### *Build Errors:*
My first attemp to build failed returning 6 errors:
1) `fatal error: PCH file 'obj/ui/views/views/precompile.h-cc.gch' not found: module file not found`
2) `fatal error: cannot open file '../../base/numerics/checked_math_impl.h': Too many open files in system`
3) `fatal error: cannot open file '../../../../../../Applications/Xcode-beta.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX11.1.sdk/usr/include/mach/mach_types.h': Too many open files in system`
3) `fatal error: 'SearchKit/SKSummary.h' file not found`
4) `fatal error: cannot open file '../../third_party/ced/src/pthread.h': Too many open files in system`
5) `fatal error: cannot open file '../../../../../../Applications/Xcode-beta.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX11.1.sdk/usr/include/mach/task_info.h': Too many open files in system`

I decide to try build again and the second time it was sucessful.
> Note: The second build displayed a smaller number of files to build, that when I compared to the number of files missing from the first build it was clear that the second build started were the first have stopped.
* [26797/49069] - First Failed Build
* [22278/22278] - Second completed Build
> Note 2: The first build took around 6 hours to complete the 26796 files, while the second build took around 18 hours for the 22278 files.
### Step 5
#### *Run Chromium*
Now running Chromium should be easy.
Run.
```
$ out/Default/Chromium.app/Contents/MacOS/Chromium
```
> Note 3: Remember to correct the PATH according to name you use for your directory.

&nbsp;

##### The application should start and the blue logo of Chromium should appear on your dock. Congrats! You built Chromium.
___
##### Important note: This walkthrough **is not in any way a substitute for the [official documentation](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md)**, my intention is only to share my experience in the hope of helping other devs facing similar problems.
___
##### As I found out more about the other processes part of contributing to the project, like the git workflow; CL submission, review and approval I will be adding my notes and tips to this repository.
