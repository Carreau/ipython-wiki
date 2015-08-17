### Using iTerm2 and an AppleScript launcher

As a keyboard geek, the following solution seems to work. (Note that this script uses [iTerm2](http://www.iterm2.com/)).

Copy the following script to a file called `ipy` in `~/bin`. To launch, hit APPLE+SPACE - this will open Spotlight window. In this window type `ipy` and hit ENTER. You should have a new iTerm window with IPython shell.

    #!/usr/bin/osascript

    -- Run ipython on a new iTerm (OS X version)
    -- See http://www.iterm2.com/#/section/documentation/scripting

    tell application "iTerm"
        activate
        set ipyterm to (make new terminal)
        tell ipyterm
            set ipysession to (make new session)
            tell ipysession
                set name to "IPython"
                exec command "/opt/local/bin/ipython"
            end tell
        end tell
    end tell

If you want VIm keybindings, add the following to your `~/.inputrc`:
   
    set editing-mode vi
    tab: complete
    set completion-ignore-case on
    set blink-matching-paren on

----

### Using homebrew and the terminal app

You can also use [homebrew](http://brew.sh) to install Python:

    brew install python
    brew linkapps python
    sudo -H pip install ipython

At that point, `/usr/local/bin/ipython` is a script that basically calls `python -m IPython` using the homebrew installed version of python instead of the OS X version of python.

----

### Using .bashrc for a quick python function or alias

With iPython installed, you can edit your .bashrc file to add a function or alias to launch iPython in various ways, such as in pylab mode or with the notebook:

    nano ~/.bashrc

now add the following (change "ipython3" accordingly):

    function pylab {
        ipython3 --pylab
    }

ctrl-o/ctrl-x to save and exit, and then re-launch Terminal to run. Now just enter "pylab" to launch iPython directly in pylab mode.

----

### Immediately open iPython (or pylab, as shown above) in the OS X Terminal

    1. Go to the Terminal's preferences
    2. Go to the Profiles section
    3. Select your desired profile (or create one for iPython)
    4. Check "Run Command" under "Startup"
    5. Enter the command "ipython[3]" or "pylab" (if you've made the above modification to .bashrc)
    6. Close the Preferences
    7. Choose Shell > New [Window/Tab] > "profile name"

When you do this, the selected profile will open and immediately go to iPython. You can further facilitate this by naming the profile "ipython" and then going to the OS X Keyboard system preferences, in which you can assign a custom hotkey to the menu name "ipython" for the Terminal application. Now when in the Terminal, pressing the hotkey will launch a new window with iPython.