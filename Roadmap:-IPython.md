This document describes the broad goals, vision and direction of the IPython project.  IPython
is a large code base with many different areas of functionality. With development ramping up
it is critically important that we focus our efforts in a disciplined manner. Can simply can't do 
everything at once.

To provide direction and focus to the project, the core IPython developers have started to meet
every six months, mostly in person, at UC Berkeley. These meetings are being funded through the
Sloan Foundation. Along with these meetings, we plan to have a major release every six months (July / December).  The meetings will happen right after each release, to provide focus and direction
for work on the next six months of work. Our first meeting was in January 2013, to plan for the 
1.0 release in July and this document is a direct result of that meeting. 

The gist of our goals for 2013-2014 is to spend 2013 really solidifying
the single-user notebook experience, which is principally in two
major areas:

- Working with the notebook document, and interacting with other formats (nbconvert)
- Interactive and dynamic content in the notebook (Javascript plugins / widgets)

Following that, we will begin in earnest the development
of the multi-user notebook.  This multi-user notebook will
be very much like the single-user notebook,
but the server will map directly onto Linux users of a single
environment, so that you only need to run a single server
for all users of your system (be it a local shared machine, EC2 image, etc.).

We plan to have a major release every six months (July / December),
hitting some milestones along the way.
Obviously, there are many other enhancements to add and bugs to fix
that we will make along the way that are not described here.
But this is the gist of our plans and priorities for the immediate term.

## Release 1.0, July 15, 2013

Milestones for IPython 1.0

Categorized by difficulty / amount of work (Easy, Medium, Hard),
and priority (0-3, with 0 being most important).
Where relevant, the principal leaders are also noted.
This is not to say they will be the only contributors,
only that they will be responsible for moving the task forward / making decisions.

- (H,0) Main work on nbconvert (Fernando, Matthias, Jonathan, Damian)
  - This is the main milestone for IPython 1.0 - the integration of nbconvert.
    The IPython notebook is a document format, and nbconvert is the principal
    tool for working with that document (static HTML export, working with Sphinx, etc.).
- (H,1) JS security (Brian, Matthias, Min)
  - We need to work out a sensible solution to sanitizing HTML and Javascript
    in notebooks.  In the long-term, the answer is going to be js plugins,
    but that work will not be ready for this release.
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
- (E,1) Emacs/VIM modes for CodeMirror (Fernando)
  - codemirror has emacs/vim bindings. Let's allow users to use them.
- (E,1) Implement Notebook autosave (Min)
  - The notebook desperately needs autosave
- (H,2) Filesystem navigation from the dashboard
  - We need UI for navigating the dashboard around the filesystem,
    so that you don't need to keep restarting the NB Server for every notebook directory.
- (M,2) raw_input (Fernando)
  - The notebook needs a basic implementation of a handler for raw_input messages.
- (M,2) Fixing random UUID issue
  - Every restart of the notebook
- (M,2) JS Testing
  - We have lots of javascript, and we need to learn about testing it.
- (E,2) Kernel autorestarting (Brian)
  - Kernels need to automatically restart without any intervention from the UI.
- (H,3) Config UI
  - Some aspects of the Javascript ought to be configurable
- (H,3) No scroll on mouse click
- (E,3) HTML Anchors
  - if we put simple HTML anchors on cells,
    it will be easy to link to a specific cell in a notebook.
- (E,3) Notebook format changes
  - There are some changes to the notebook format needed,
    most notably, perhaps, being the removal of worksheets.
    We have a model in mind for heirarchical (including tabbed)
    views of a single notebook based on Sections,
    denoted by Header cells.
    This will be much more flexible than hard-coded worksheets,
    which have still received no support in the HTML UI,
    despite being in the document format since the beginning.
- (E,3) Column width (after bootstrap)
  - The notebook currently fills the width of the screen


## Release 2.0, December 25, 2013

Second major release milestone

- Polish work on nbconvert, started in 1.0
- Work on interactive widgets, and the JSON/handler messages,
  necessary for rich interactive content.

## Release 3.0, July 15, 2014

Third major milestone

- Further develop Interactive widgets and non-Python kernels
- Initial multi-user notebook
  - The multi-user IPython Notebook will map 1:1 to a Linux user environment
    with existing, trusted users.
    The notebook server will be run as root,
    and kernels run as individual users,
    with the full rights, access, and privileges of those users on the system.


## Release 4.0, December 25, 2014

This marks the end of the Sloan Foundation funding,
by which point we should have a pretty solid multi-user
IPython Notebook server with interactive widgets,
and powerful utilities for interacting with other formats,
such as generating Sphinx / HTML or LaTeX / PDF documents
from IPython Notebooks, and running code in
at least a few languages (Python, Ruby, R, etc.).

Obviously, we will never run out of things to do on IPython,
but this is our current plan,
and we are excited to see where IPython takes us.