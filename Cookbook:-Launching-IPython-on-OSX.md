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