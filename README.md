# CHECKOUT AND BUILD CHROMIUM
[Official Checking out and building Chromium for Mac](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md#System-requirements).
## My experience and tips

The process of Checkout and Build for Chromium is straightforward, but due to my machine's configuration I had to take several steps to complete the process. This walthrough **is not in anyway a substitute for the [oficial documentation](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md)**, my intention is only to share my experience in the hope of helping other devs facing similiar problems.
#### *My Setup:*
* MacBook Air (13-inch, 2017)
* 1.8 GHz Dual-Core Intel Core i5
* 8 GB 1600 MHz DDR3
* Storage: The more the better. I had 50gb available on my Macbook and I'm using an external HD with 3T for the codebase.
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
> note: If you like myself have **CommandLineTools** installed, the command return "not found" and you will have to download Xcode anyway. So go ahead and do it, the file require aproximately 42gb and it takes sometime, you can continue while Xcode download. More about possible bugs that arrise from this on step number XX.
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
> note: you must use the absolute path or Python will not be able to find infra tools. Here is an example of how my absolute path looks like `/Users/diegoosvaldodemoura/depot_tools`.
> tip: Right click the depot_tools folder (command + i) and copy all the content of the field **where**. That's your PATH to the folder and by pasting it into the terminal in the correct spot of the command, and the shell will take care of making the corrections (like removing the MacintoshHD from the PATH.)
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
> note: If the command don't work, there is probably something wrong with your PATH. The fetch tool is part of the depot_tools and can only run if you PATH has the directory at the end. Visit the offical page for more information [Official Checking out and building Chromium for Mac](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md#System-requirements).
> note 2: The first time I did the checkout process it took around 2hours and 30 minutes (8-10:30 pm). The second time I did the same process starting at 8 am and it took 6 hours. So be aware of the time of the day you chose to work on this, as it seems that the later at night where traffic is lower the speed is much better.

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
> note: that **Default** may be changed for whatever you like, but not the **out/** path. Visit the offical page for more information [Official Checking out and building Chromium for Mac](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md#System-requirements).
#### *Error handling:*
If you get the error:
```
    xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance
```
This means that you previously installed CommandLineTools is blocking the PATH and the depot_tools can't communicate with the new *xcode-select* from you newly intalled Xcode app. 
Fixing it:
1) Ensure Xcode app is in the `/Applications` directory (NOT `/Users/{user}/Applications`)
2) Run:
```
    sudo xcode-select -s /Applications/Xcode.app/Contents/Developer`
```
3) Make sure you have the correct version of Xcode in the PATH (eg. `Xcode.app`, `Xcode-beta.app`)
4) All done, run the build command again.
> tip : Xcode crashed when I ran the build command, returning an error. Still, the built initialization was sucessful. So if that happens to you, make sure to check the command line for the output `Done. Made xxxx targets from xxxx files in xxxx.ms` before you get frustrated and close the terminal on a built that is happening despite Xcode errors.
> tip 2: Official guide on strackOverflow on how to correct the xcode-select PATH [here](https://stackoverflow.com/questions/17980759/xcode-select-active-developer-directory-error/17980786#17980786).


