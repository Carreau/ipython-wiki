## Sophos Heisenbugs (Windows) 

Very frequently, Sophos causes IPython Notebook communications to fail without
reporting any kind of error or feedback. It does not seem like there is way on
our end to catch this.

Here are the issues filed for this kind of behavior:

* [#3311](https://github.com/ipython/ipython/issues/3311)  (closed) 
* [#2499](https://github.com/ipython/ipython/issues/2499)  (open) 

Starting up a notebook works, kernels start, but no execution can be issued
from the notebook and the UI just stalls on `In[*]`. Starting up an `ipython
console --existing` or `ipython qtconsole --existing` works without problems.
However, trying to execute things from the notebook becomes a no-op (with that
said, I seem to recall times when the execution requests go through, and only
the output is not returned) 

### The solution with the most reported successes

From [StackOverflow user Joran Beasley](http://stackoverflow.com/questions/13036197/ipython-notebook-getting-output/13075334#13075334) 

1. open your Sophos Endpoint Security and Control Panel from your tray or start menu
1. Select "configure" > "Anti-virus" > "Authorization" from the menu at the top
1. Select the websites tab
1. click the "Add" button and add 127.0.0.1 to the "Authorized websites" list
1. restart computer

output works now :)

### Another possible solution

Provided by [@tritemio on GH #3311](https://github.com/ipython/ipython/issues/3311)

1. Open "Sophos Endpoint Security and Control" from the taskbar icon
1. Follow the menu Configure -> Anti-Virus -> Web protection...
1. In the option Block access to malicious websites select off. After that
reboot, **doesn't work without rebooting!**

### Sometimes, neither of these solutions work

Had an experience today (2013-08-26) on Windows 8 where we tried both of the
above solutions, rebooting after each, and still couldn't punch through the
Sophos firewall. Kill all Sophos processes *did* finally resolve the issue,
however, so we know it was Sophos all along, just couldn't find a way to coerce
it into playing nicely.
