This document describes the goals, vision and direction of the IPython project.  IPython
is a large project with many different areas of functionality. With development ramping up
it is critically important that we focus our efforts in a disciplined manner. We simply can't do 
everything at once.

To provide direction and focus to the project, the core IPython developers have started to meet
every six months, mostly in person, at UC Berkeley. These meetings are being funded through the
Sloan Foundation. Along with these meetings, we plan to have a major release every six months (roughly July / December).  The meetings will happen around each release, to provide focus and direction
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

## Release 1.0, August 8, 2013

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
- (H,1) Notebook toolset changes
  - [PR #3240](https://github.com/ipython/ipython/pull/3240)
  - bootstrap
      - [PR #3232](https://github.com/ipython/ipython/pull/3232)
  - less
      - [PR #3325](https://github.com/ipython/ipython/pull/3325)
  - CodeMirror3
      - [PR #3232](https://github.com/ipython/ipython/pull/3232)
  - require.js
      - [PR #3364](https://github.com/ipython/ipython/pull/3364)
- (M,1) Organization of JS/CSS (Brian)
  - [PR #3321](https://github.com/ipython/ipython/pull/3321)
  - [PR #3325](https://github.com/ipython/ipython/pull/3325)
- (M,1) General CSS cleanup (Brian, Matthias)
  - These three are really one big project of cleaning up and reorganizing
    the non-Python code.  We are going to adopt some new tools
    for managing js installation and dependencies,
    and start to make the IPython js more reusable outside of IPython.
  - [PR #3325](https://github.com/ipython/ipython/pull/3325)
- (M,1) Message protocol changes (Min)
  - Our first go at the message protocol has gotten us pretty far,
    but we have found a few places we need to make some changes
    for the long term.
  - image size metadata ([PR #3190](https://github.com/ipython/ipython/pull/3190))
  - `user_expressions` / `user_variables` should use rich display system ([PR #3319](https://github.com/ipython/ipython/pull/3319))
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
  - temporary solution [for custom.js](https://gist.github.com/minrk/5940801)
- (E,1) Implement Notebook autosave (Min)
  - The notebook desperately needs autosave
  - [IPEP 15](IPEP-15%3A-Autosaving-the-IPython-Notebook)
  - [PR 3158](https://github.com/ipython/ipython/pull/3158)
- (M,2) raw_input (Min)
  - The notebook needs a basic implementation of a handler for raw_input messages.
  - [PR #3089](https://github.com/ipython/ipython/pull/3089)
- (M,2) JS Testing
  - We have lots of javascript, and we need to learn about testing it.
- (E,2) Kernel autorestarting (Brian)
  - Kernels need to automatically restart without any intervention from the UI.
  - [PR #3011](https://github.com/ipython/ipython/pull/3011)
- (H,3) No scroll on mouse click (Brian)
- (E,3) HTML Anchors
  - if we put simple HTML anchors on cells,
    it will be easy to link to a specific cell in a notebook.
  - [PR #3064](https://github.com/ipython/ipython/pull/3064)
  - [PR #3058](https://github.com/ipython/ipython/pull/3058)
- (E,3) Column width (after bootstrap)
  - The notebook currently fills the width of the screen
  - [PR #3393](https://github.com/ipython/ipython/pull/3393)

## Release 2.0, April 1, 2014

Development focus for IPython 2.0:

- Polish work on nbconvert, started in 1.0
- Work on interactive widgets, and the JSON/handler messages,
  necessary for rich interactive content.
- rework URL scheme to split REST api requests from human HTML pages


### Moved from 1.0

- (H,3) Config UI
  - Some aspects of the Javascript ought to be configurable
- (H,2) Filesystem navigation from the dashboard
  - We need UI for navigating the dashboard around the filesystem,
    so that you don't need to keep restarting the NB Server for every notebook directory.
- (M,2) Fixing random UUID issue
  - Every restart of the notebook, notebooks get new UUIDs
  - [PR #3140](https://github.com/ipython/ipython/pull/3140)
  - [IPEP 16](IPEP-16%3A-Notebook-multi-directory-dashboard-and-URL-mapping)
- (M, 1) Message spec changes
  - some message spec changes proposed in 1.0 have nbformat implications, and will be held until those changes are made
  - remove the few python-specific idioms (`pyin`, `pyout`)
  - JSON messages
- (E,3) Notebook format changes
  - There are some changes to the notebook format needed,
    most notably, perhaps, being the removal of worksheets.
    We have a model in mind for hierarchical (including tabbed)
    views of a single notebook based on Sections,
    denoted by Header cells.
    This will be much more flexible than hard-coded worksheets,
    which have still received no support in the HTML UI,
    despite being in the document format since the beginning.

### JS widget/plugins
- (E,0) Implement prototype of message spec (Min)
  - Decide on the proper naming of things
  - update state, create widget, destroy widget
  - With toy examples
- (M,0) Implement high-level Python UI (Paul, Matthias, Brian)
  - `interact` and `interactive`
  - Python classes for UI controls
- (M,0) Basic UI controls in JS (slider, checkbox, textarea, dropdown, button, radio) (Jon, Matthias, Brian)
- (M,1) Installation: find a consistent place for people to put JavaScript code (.ipython/static) (Min)
- (M,1) Figure out how to get JavaScript code on the page for widgets (Min)
- (M,1) Design API for widgets that use JSON embedded in Notebook without a running kernel. This will help us solve the persistence problem (Jon)

### Security
- (H,0) Sanitize Markdown cells always (Brian)
- (H,0) Sanitize all output on page load (Brian)
- (H,3) Think about removing the ability to execute dynamically generated JavaScript code?

### Core
- (E,0) Drop 2.6 support (Thomas)
- (E,0) Drop 3.2 support (Thomas)
- (M,1) Remove language magics from ipython/ipython (Paul)
- (E,2) Removal of unused code (Thomas)
- (M,2) Represent exception with JSON, working with Julia devs and message spec (Fernando)
- (M,2) config: append/extend/update (Min)
- (M,2) Get rid of aliases (Fernando, Thomas)
- (H,2) get rid of 2to3 (Thomas)
- (M,3) Remove all hardcoding ANSI coloring and replace with semantic markup that is interpreted by frontends (Carlos)

### Testing
- (M,0) Add tests for notebook web services (Zach, Min)
- (M,0) Add Phantom/Casper based Notebook tests (Paul, Brian)
- (M,0) JavaScript unit tests (Paul, Brian)
  - Coverage reports
- (M,1) Integrating coverage reports into iptest (Thomas)
  - Integrated coverage report of the entire test suite, not just the components
- (M,1) Coverage into Travis for PRs (Thomas)
- (M,2) Refactor/cleanup iptest (Jon vs Thomas vs Paul)
  - Turn iptest into subcommand
- (M,2) Validate notebooks to make sure they adhere to our JSON spec (Matthias)
- (M,2) Improve testing of message spec (Min)
  - clients
  - kernels
    - wrapper script to use the test suite as an instrumented probe against a kernel. Can be subcommand
  - zmq man-in-the-middle validator for messaging traffic.

### Docs
- (M,1) Build conceptual framework for documentation (Brian, David S.)
  - Outline the overall structure
  - Identify Notebooks that are the leaves of the tree/outline
  - Develop spec for each Notebook
- (E,2) Change filename extension from `.txt` to `.rst` (Paul)
- (M,2) How do we document our JavaScript code? (Brian)
- (HHH,2) Write Notebook based documentation as Notebooks in IPython source tree
- (E,3) Defining vocabulary
  - Server, Web application, Document
  - "Notebook" versus "notebook"?
  - What do we call the "interactive computing protocol", "interactive computing kernel", etc
- (H,3) Provide search and indexing for our Notebook documentation. As an MVP just export to Sphinx and allow searching through that
- (H,3) Expose our documentation as directories in Notebook Dashboard
- (H,3) Develop semantics for copy on write saving of documentation Notebooks
- What will be do about API documentation and how will we integrate that into our Notebook document

### Message Spec
- (E,1) Rename `pyout` and `pyin` (Min)
- (E,1) Remove `user_variables` from the `execute_request` message?  Make sure `user_expressions` returns a dict.  We currently have no policy for error handling of these requests. (Min)
- (M,1) Review the message spec in light of the Julia kernel work
  - The `complete_request` message could likely be simplified (ex, does it need the `text` key?) (Min)
- (E,2) Consistently represent empty fields in the message spec, especially docstrings (Min)
- (E,2)  In Python code the JSON repr should be a dict, not a string.  In the message spec, JSON reprs should be live JSON objects, not JSON strings. (Jess, Zach)

### nbconvert
- (E,0) Communicate to everyone that nbconvert is completely unstable, especially at the API level (Jon)
- (E,2) Work with Dexy to improve the integration with nbconvert (Paul)
  - Making sure our utilities for formatting are available in dexy
  - Converting input/ouput to HTML/LaTeX in the IPython style
- (M,2) Develop web service API for nbconvert (Jon, Matthias, Zach)
- (M,3) Expose nbconvert UI in Notebook that uses the nbconvert web service
- (M,3) Move .py export to nbconvert and remove `--script`

### nbviewer
- (E,0) Rewrite using tornado to improve performance (Min)
  - Our current bottleneck is waiting for GitHub API requests for Notebook contents
- (E,0) Rewrite to use the latest nbconvert and nbformat (Jon)
- (E,2) Add support for CSS metadata to nbviewer (Matthias)
- (H,2) Add reveal.js slideshow mode to nbviewer (Damian)
- (H,3) Allow Notebooks to be downloaded in other formats 
- (H,3) Create nbconvert as a JSON based web service
  - Ex: post a .ipynb, get a page back
- (H,3) Change hosting to Rackspace
  - in a PersonalCloud VM easy to deploy ?

### nbformat
- (E,0) Document how we write the JSON data structure on-disk, including our homebrew "multiline strings in JSON" which we store as a list of strings (Min)
- (E,1) Encode language/kernel information in main notebook metadata (Jess, Matthias)
  - Remove language from cell-level metadata
- (E,1) Remove name from metadata (Jess, Matthias)
- (E,1) Removal of worksheets (Jess, Matthias)
  - We have a model in mind for hierarchical (including tabbed) views of a single notebook based on Sections, denoted by Header cells. This will be much more flexible than hard-coded worksheets, which have still received no support in the HTML UI, despite being in the document format since the beginning.
- (H,1) Version conversions done right (Jon, Matthias)
- Arbitrary mimetypes in output:
  - (E,1) Having notebook output cells reflect raw mimetype info from kernels (Min)
  - (M,2) In python api, we have mimetype formatters: better expose an api for python objects to publish arbitrary mimetypes (Min)
- Notebok validation: 
  - (H,2) propagation of error info (Jon, Jess, Matthias)
  - (H,3) the ability to get a notebook that is at least valid JSON as an object.
- (E,3) Think about changing the file extension from `.ipynb` to something else.  We like the name `.inb`.

### Notebook
- (E,0) Standardize on cell level metadata: name, tags. Document this. (Paul, Matthias)
- (H,0) Dual mode UX (Brian)
- Multi-directory support
  - (M,0) Review Zach's PR (Thomas, Brian)
  - (M,0) Add navigation UI (Zach, Matthias)
- UI to edit Notebook metadata
  - (E,2) MVP: raw edit mode with check for valid JSON (Matthias)
- CSS
  - (E,2) Create Notebook level CSS metadata that points to the URL of CSS files to load (Jon, Matthias)
  - (E,2) Create UI to refresh the notebook style (Jon, Matthias)
  - (H,2) Clean up our CSS classes. Use names like `ipy-cell-input`. This should be done to create a "public" API for styling notebooks (Jon)
- (M,2) Notebook config (Jon)
  - Represent settings in a JSON data structure, maybe in a cookie
  - Write UI code to r/w the settings
- (H,2) Design and typography (Brian, Zach, Jess, David S.)
  - Reduce vertical size of dashboard item
  - Reduce vertical size of heading cells
  - Think about increasing font size of Markdown cells
  - Think about pager layout and side panels
  - (side note: http://www.amazon.com/Design-Hackers-Reverse-Engineering-Beauty/dp/1119998956/ )
- (E,3) Make notebooks importable
- Multi-kernel support
  - (E,3) Add kernel selection to /api/kernels web service
  - (E,3) Add kernel selecction logic to MultiKernelManager
  - (M,3) Ability to select the kernel type of a new or existing notebook
  - (M,3) Ability to change the kernel type of a running notebook
- (M,3) Notebook should highlight code cells accordiing to the kernel language
- (M,3) Command line entry point: `ipython notebook mynotebook.ipynb` that links to existing server if available, otherwise starts a new one.
- (H,3) Think more about Notebooks connecting to existing, active, kernels
- (H,3) Finish IPEP 12
- (H,3) Literate programming as kernel variables in Markdown cells
  - Better pipeline for adding hooks to Markdown rendering
  - What syntax do we use?

### Parallel
- (E,2) python -m (Min)
- (M,2) Application refactoring (Min)
- (M,3) Futures
- (H,3) Add node-to-node communicators and channels
- (H,3) Distributed objects


While we haven't really begun this work, there are some things we are thinking about that will affect users of the Notebook, in particular people who are already developing JavaScript widgets for the Notebook.

- A number of people have written JavaScript widgets that startup an additional server that talks to the JavaScript side using WebSockets.  Using additional server logic as you have done is not officially
supported.  The notebook architecture may change in a way that makes it impossible to do this type of thing.
The problem is that these additional servers make some strong assumptions about where the notebook server, kernel and your server are running.  The preferred way of getting data back and forth between python and the
browser is to use our message channels.  Currently these channels are not sufficient for many interactive widgets, but after the 2.0 release, they will be.
- Because of security issues are are moving away from the notebook being able to execute dynamically generated JavaScript code.  This removal will actually happen in the 1.0 release (see above), but the full replacement with a JavaScript plugin system won't happen until 2.0.
- Because of these changes developers who are writing JavaScript widgets that work with the current IPython should expect to rewrite them for the 2.0 release.

## Release 3.0, Summer, 2014

Development focus for IPython 3.0:

- Further develop Interactive widgets and non-Python kernels
- Initial multi-user notebook
  - The multi-user IPython Notebook will map 1:1 to a Linux user environment
    with existing, trusted users.
  - The notebook server will be run as root, and kernels run as individual users,
    with the full rights, access, and privileges of those users on the system.


## Release 4.0, Winter, 2014

This marks the end of the Sloan Foundation funding, by which point we should have a
solid multi-user IPython Notebook server with interactive widgets, and powerful utilities
for interacting with other formats, such as generating Sphinx / HTML or LaTeX / PDF documents
from IPython Notebooks, and running code in at least a few languages (Python, Ruby, R, etc.).