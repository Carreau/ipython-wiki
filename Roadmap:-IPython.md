This document describes the goals, vision and direction of the IPython project.  IPython
is a large project with many different areas of functionality. With development ramping up
it is critically important that we focus our efforts in a disciplined manner. We simply can't do 
everything at once.

To provide direction and focus to the project, the core IPython developers have started to meet
every six months, mostly in person, at UC Berkeley. These meetings are being funded through the
Sloan Foundation. Along with these meetings, we plan to have a major release every six months (July / December).  The meetings will happen right after each release, to provide focus and direction
for the next six months of work. Our first meeting was in January 2013, to plan for the 
1.0 release in July and this document is a direct result of that meeting. Each meeting will
result in this document being updated with a roadmap for each release.

Below you will find our upcoming releases and a list of the tasks we will focus
our development efforts on for that release. These tasks categorized by difficulty / 
amount of work (Easy, Medium, Hard), and priority (0-3, with 0 being most important).
Where relevant, the developers leading the work are also noted. This is not to say
they will be the only contributors, only that they will be responsible for moving
the task forward. If you are interested in working on one of these tasks, please coordinate
with these individuals.

For the most part, we would like developers to focus on the top priority tasks, before
moving on to the lower priority ones. This is not to say that things will never be done
out of order, but our priorities have been chosen carefully, taking into account dependencies
between different tasks. If developers contribute new features outside the focus of the current
release, it is likely that we will ignore their contributions until a later release.
Work on bug fixes will always be given high priority.

## Overview of 2013-2014

Our work for 2013-2014 is being driven by our grant from the Sloan Foundation. 
More details of this grant can be found [here](http://ipython.org/sloan-grant.html).
For 2013, we are going to focus on really solidifying the single-user notebook experience, 
which is principally in two major areas:

- Working with the notebook document, and interacting with other formats (nbconvert)
- Interactive and dynamic content in the notebook (Javascript plugins / widgets)

Following that, in 2014, we will begin the development of the multi-user notebook.
This multi-user notebook will be similar to the single-user notebook, but the server will
map directly onto Linux users on a single environment, so that you only need to run a single server
for all users of your system (be it a local shared machine, EC2 image, etc.). This multi-user notebook
server will be focus on the usage case of small-medium sized groups of trusted users. It will not
have features that allow it to scale to large numbers of users on the open internet.

## Release 1.0, July 15, 2013

Development focus for IPython 1.0:

- (H,0) Main work on nbconvert (Fernando, Matthias, Jonathan, Damian)
  - This is the main milestone for IPython 1.0 - the integration of nbconvert.
    The IPython notebook is a document format, and nbconvert is the principal
    tool for working with that document (static HTML export, working with Sphinx, etc.).
- (H,1) JS security (Brian, Matthias, Min)
  - We need to work out a sensible solution to sanitizing HTML and Javascript
    in notebooks.  In the long-term, the answer is going to be js plugins,
    but that work will not be ready for this release.
  - [PR #3101](https://github.com/ipython/ipython/pull/3101)
- (H,1) Notebook toolset changes (bootstrap, less, require, ACE, CM3).
- (M,1) Organization of JS/CSS (Brian)
- (M,1) General CSS cleanup (Brian, Matthias)
  - These three are really one big project of cleaning up and reorganizing
    the non-Python code.  We are going to adopt some new tools
    for managing js installation and dependencies,
    and start to make the IPython js more reusable outside of IPython.
- (M,1) Message protocol changes (Min)
  - Our first go at the message protocol has gotten us pretty far,
    but we have found a few places we need to make some changes
    for the long term.
    Specific examples:
      - remove the few python-specific idioms (`pyin`)
      - remove `pyout` in favor of adding necessary info to nearly-identical `display_data`
      - image size metadata
      - multiple entities in a single message (e.g. display list of images)?
      - adjust format of results of user-expressions/variables queries to better represent errors.
      - JSON messages (probably won't be ready for this release)
- (M,1) Documentation (Paul)
  - Our documentation is in need of a lot of work,
    and Paul is going to help develop a fresh view of how we should
    organize our docs, specifically splitting the Sphinx API docs
    from Notebook-based narrative docs.
- (M,1) Splitting the KernelManager (Min, Brian)
  - The KernelManager is currently two objects in one - a Manager for starting/stopping/monitoring
    a kernel subprocess, and a Client for interacting with the kernel via zmq.
    KM will be split into two objects, and a Control channel added, enabling
    signaling / interrupting / restarting of kernels from remote clients.
    This will extend to Engines in the long term, but not 1.0.
  - [PR 3011](https://github.com/ipython/ipython/pull/3011)
- (E,1) Emacs/VIM modes for CodeMirror (Fernando)
  - codemirror has emacs/vim bindings. Let's allow users to use them.
- (E,1) Implement Notebook autosave (Min)
  - The notebook desperately needs autosave
  - [IPEP 15](IPEP-15%3A-Autosaving-the-IPython-Notebook)
  - [PR 3158](https://github.com/ipython/ipython/pull/3158)
- (H,2) Filesystem navigation from the dashboard
  - We need UI for navigating the dashboard around the filesystem,
    so that you don't need to keep restarting the NB Server for every notebook directory.
- (M,2) raw_input (Min)
  - The notebook needs a basic implementation of a handler for raw_input messages.
  - [PR #3089](https://github.com/ipython/ipython/pull/3089)
- (M,2) Fixing random UUID issue
  - Every restart of the notebook, notebooks get new UUIDs
  - [PR #3140](https://github.com/ipython/ipython/pull/3140)
  - [IPEP 16](IPEP-16%3A-Notebook-multi-directory-dashboard-and-URL-mapping)
- (M,2) JS Testing
  - We have lots of javascript, and we need to learn about testing it.
- (E,2) Kernel autorestarting (Brian)
  - Kernels need to automatically restart without any intervention from the UI.
  - [PR #3011](https://github.com/ipython/ipython/pull/3011)
- (H,3) Config UI
  - Some aspects of the Javascript ought to be configurable
- (H,3) No scroll on mouse click
- (E,3) HTML Anchors
  - if we put simple HTML anchors on cells,
    it will be easy to link to a specific cell in a notebook.
  - [PR #3064](https://github.com/ipython/ipython/pull/3064)
  - [PR #3058](https://github.com/ipython/ipython/pull/3058)
- (E,3) Notebook format changes
  - There are some changes to the notebook format needed,
    most notably, perhaps, being the removal of worksheets.
    We have a model in mind for hierarchical (including tabbed)
    views of a single notebook based on Sections,
    denoted by Header cells.
    This will be much more flexible than hard-coded worksheets,
    which have still received no support in the HTML UI,
    despite being in the document format since the beginning.
- (E,3) Column width (after bootstrap)
  - The notebook currently fills the width of the screen


## Release 2.0, December 25, 2013

Development focus for IPython 2.0:

- Polish work on nbconvert, started in 1.0
- Work on interactive widgets, and the JSON/handler messages,
  necessary for rich interactive content.

While we haven't really begun this work, there are some things we are thinking about that will affect users of the Notebook, in particular people who are already developing JavaScript widgets for the Notebook.

- A number of people have written JavaScript widgets that startup an additional server that talks to the JavaScript side using WebSockets.  Using additional server logic as you have done is not officially
supported.  The notebook architecture may change in a way that makes it impossible to do this type of thing.
The problem is that these additional servers make some strong assumptions about where the notebook server, kernel and your server are running.  The preferred way of getting data back and forth between python and the
browser is to use our message channels.  Currently these channels are not sufficient for many interactive widgets, but after the 2.0 release, they will be.
- Because of security issues are are moving away from the notebook being able to execute dynamically generated JavaScript code.  This removal will actually happen in the 1.0 release (see above), but the full replacement with a JavaScript plugin system won't happen until 2.0.
- Because of these changes developers who are writing JavaScript widgets that work with the current IPython should expect to rewrite them for the 2.0 release.

## Release 3.0, July 15, 2014

Development focus for IPython 3.0:

- Further develop Interactive widgets and non-Python kernels
- Initial multi-user notebook
  - The multi-user IPython Notebook will map 1:1 to a Linux user environment
    with existing, trusted users.
  - The notebook server will be run as root, and kernels run as individual users,
    with the full rights, access, and privileges of those users on the system.


## Release 4.0, December 25, 2014

This marks the end of the Sloan Foundation funding, by which point we should have a
solid multi-user IPython Notebook server with interactive widgets, and powerful utilities
for interacting with other formats, such as generating Sphinx / HTML or LaTeX / PDF documents
from IPython Notebooks, and running code in at least a few languages (Python, Ruby, R, etc.).