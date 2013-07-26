This page serves to index the various IPython Enhancement Proposals that we have sed and are using to guide IPython development.  Developers are encouraged to author IPEPs for any development in IPython that the community needs to discuss *before* code is written.  Currently, our protocol for IPEPs is relatively simple.  

The IPEP author should:

1. Create a new wiki page with the title "IPEP N: Title" where "N" is the next consecutive IPEP number and "Title" is an appropriate title for the IPEP.
2. Link to that wiki page from this page.
3. Create a GitHub issue for the IPEP.
3. Notify ipython-dev that you have created the IPEP and inform them that discussion is taking place
   on this issue (provide links to the IPEP and issue).
5. Discussion should continue until consensus is reached.
6. At that point, a pull request or issue should be opened to track the actual implementation.
7. The IPEP wiki page should be updated with information about the discussion and implementation.

Each IPEP should have a table/area at the top with the following information:

<table>
<tr><td> Status </td><td> Active|Deferred|Accepted|Rejected|Implemented </td></tr>
<tr><td> Author </td><td> Full Name &lt;full.name@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> April 6, 2013</td></tr>
<tr><td> Updated </td><td> April 26, 2013</td></tr>
<tr><td> Discussion </td><td> link to the issue where the IPEP is being discussed </td></tr>
<tr><td> Implementation </td><td> link to the PR </td></tr>
</table>

# Index of IPEPs

* [IPEP 1: Cleanup and extension of the Magic system in IPython]( https://github.com/ipython/ipython/issues/1611) -- *Done*
* [IPEP 2: Input transformations](https://github.com/ipython/ipython/issues/2293) -- *Done*
* [IPEP 3: Multiuser support in the notebook](https://github.com/ipython/ipython/wiki/IPEP-3:-Multiuser-support-in-the-notebook) -- *Planned for 3.0*
* [IPEP 4: Python 3 Compatibility](https://github.com/ipython/ipython/wiki/IPEP-4:-Python-3-Compatibility) -- *Done*. Will drop support for 3.2?
* [IPEP 5: Notebook JavaScript organization](https://github.com/ipython/ipython/wiki/IPEP-5:-Notebook-JavaScript-organization) -- *Planned for 2.0*
* [IPEP 6: Qt console - additional pane](https://github.com/ipython/ipython/wiki/IPEP-6:-Qt-console---additional-pane) -- *Open*
* [IPEP 7: Subclients for the Notebook](https://github.com/ipython/ipython/wiki/IPEP-7:-Subclients-for-the-Notebook) -- Replaced by [[IPEP 18]]
* [[IPEP 8: Custom messages and message handlers|IPEP 8: Custom messages and message handlers]] -- *Progress planned for 2.0*
* [IPEP 9: Slideshow from the notebook](https://github.com/ipython/ipython/wiki/IPEP-9:-Slideshow-from-the-notebook) -- Reveal and LiveNotebook are usable. *Stable in 2.0*
* [[IPEP 10: Kernel-side filtering of display formats|IPEP-10:-kernel-side-filtering-of-display-formats]] -- *???*
* [[IPEP 11: Tab-completion system refactor|IPEP-11:-Tab-Completion-System-Refactor]] -- *A lot of work; planned for 3.0*
* [[IPEP 12: Splitting KernelManager and KernelClient]] -- *???*
* [[IPEP 13: Updating the Message Spec]] -- *Planned for 2.0*
* [[IPEP 14: Notebook Regression Tests]] -- *???*
* [[IPEP 15: Autosaving the IPython Notebook]] -- *Done*
* [[IPEP 16: Notebook multi-directory dashboard and URL mapping]]
* [[IPEP 17: Notebook format 4]]
* [[IPEP 18: Text editor for the Notebook]]
* [[IPEP 19: `concurrent.futures` (PEP 3148) for `IPython.parallel`]]
* [[IPEP 20: Refactor magics into thin wrappers around Python libraries]]