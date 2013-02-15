IPython Overview
----------------

1.  Core IPython experience

1.  Special syntax
2.  Kernel execution loop

2.  Parallel

1.  Rewrite Scheduler, allow sharding
2.  Client caching
3.  PEP-3148

3.  Frontends

1.  Terminal and console
2.  Qt
3.  Notebook
4.  Web console: future

4.  Message Protocol
5.  Notebook format^[[a]](#cmnt1)^

1.  nbconvert
2.  nbformat

6.  Documentation -\> Paul

1.  Narrative docs (100% notebooks)
2.  Separate API docs
3.  JS into API docs (YUI doc + Sphinx)

7.  Marketing/Outreach

1.  Stickers! mugs, posters, t-shirts
2.  Communicating with the public (twitter, google plus, project blog,
    wiki)
3.  PyCon booth

8.  Third party projects:

1.  emacs notebook client
2.  vim client
3.  MS visual studio python tools
4.  IdleX
5.  Spyder
6.  Ninja IDE (as plugin)
7.  Sage^[[b]](#cmnt2)^
8.  Sympy

9.  nbviewer

1.  link in the notebook
2.  nbconvert webservice

10. testing

1.  JS scripting?

Release planning
----------------

1.0 Release (July 15, 2013) (Easy, Medium, Hard) (Priority 0,1,2,3; 0
most important)

1.  0 - Paul’s PhD
2.  (H,0) Main work on nbconvert (Fernando, Matthias, Jonathan)
3.  (E,1) Emacs/VIM modes for CodeMirror (Fernando)
4.  (M,1) Organization of JS/CSS (Brian, Matthias)
5.  (M,1) General CSS cleanup (Brian, Matthias)
6.  (M,1) Message protocol changes (Min)
7.  (M,1) Documentation (Paul)
8.  (H,1) JS security (Matthias, Brian)
9.  (M,1) Splitting the KernelManager (Min, Brian)
10. (H,1) Notebook toolset changes (bootstrap, less, require, ACE, CM3).
11. (E,1) Implement Notebook autosave (Min)
12. (M,2) raw\_input
13. (H,2) Filesystem navigation from the dashboard (Zach)
14. (E,2) Kernel autorestarting (Brian)
15. (M,2) Fixing random UUID issue
16. (E,3) Anchors
17. (E,3) Notebook format changes
18. (M,2) JS Testing
19. (H,3) Config UI
20. (E,3) Column width (after bootstrap)
21. (H,3) No scroll on mouse click

2.0 Release (December 25, 2013)

1.  Finish work on nbconvert
2.  Work on interactive widgets.

3.0 Release (July 1, 2014)

1.  Multiuser notebook
2.  Interactive widgets

4.0 Release (December 25, 2014)

1.  Multiuser notebook

Discussed
=========

General Topics
--------------

1.  Release/planning cycle

1.  6 Months release cycle
2.  Release happens before next planning meeting
3.  Planning meeting to plan work for next release
4.  Next release will be 1.0!
5.  Tentatively July 15th release for 1.0.
6.  Developer starts M-F July 15-19th.

2.  Communicating release/planning

1.  Wiki page for each release detailing what we will do.
2.  Use GitHub milestones

3.  Separate IPython into multiple repositories

1.  Thinking about it for the future, maybe a few releases away.
2.  Frontends, parallel, kernel?.

4.  GitHub issue workflow for feature requests

1.  Use wishlist milestone for issues that have not been promoted to an
    active status.
2.  By default, anytime an issue is opened, the core developers target
    the issue to an active milestone or wishlist.
3.  Wishlist milestones should not be closed - if they are they should
    get milestone None.

5.  Write contributing.md for GitHub
6.  HipChat or Campfire?

1.  Much better experience than IRC.
2.  Costs money though for “persistent people”.
3.  Post to ipython-dev/ipython-user announcing our plans.
4.  Getting paid account is like getting commit rights - based on
    involvement.
5.  Post link to guest accounts on main ipython webpage.
6.  remove IRC from main ipython page at the same time.

7.  Fernando will get back into
    [ipython@gmail.com](mailto:ipython@gmail.com) and we will publicize
    that as the project contact address. We will also use this for
    project accounts on things like AWS. He will send the info to the
    core devs.

Notebook format
---------------

1.  nbconvert (Fernando, Matthias)

1.  converters

1.  markdown: redo the markdown converter.
2.  pure html:
3.  pure latex: with different styles.
4.  Sphinx Friendly reST.
5.  reveal.js
6.  blogging engines (optional).

2.  Jinja branch

1.  Transforms are applied to notebook before conversion.
2.  Filters are applied to cells.
3.  Need to look at converted output to check it.
4.  Write test
5.  Write documentation

3.  Damian’s reveal branch

1.  Merge into nbconvert master.
2.  Matthias will rebase and continue in his Jinja2 branch.

4.  Proper package structure.
5.  setup.py scripts

1.  move to the merge as fast as possible, so it becomes a part of
    ipython’s setup.py
2.  no files outside of nbconvert/ directory

6.  Integration

1.  Put into IPython proper
2.  Integrate into Notebook web service and Notebook UI

2.  nbformat

7.  Prompt number should only be empty or a number (web UI artifact).
8.  Create wiki page for proposed notebook format changes.

1.  Change attribute name from input-\>content for all cells.
2.  Remove worksheets.

9.  allow the user to easily save as an older version (previous format)
    from the Notebook UI (save as v3, save as v2)
10. title <-\> filename discrepancy discussion (keep things as is,
    document it)
11. Notebook metadata that specifies the kernel that should be used.

3.  nbviewer (Matthias)

1.  url scheme (nbviewer.org/username/ gist of people’s ipynb files)
2.  nbviewer.ipython.org should stay, or at least be a redirect
3.  nbviewer roadmap on the wiki
4.  notebook “dummy” that’s half functional? let’s leave it to the
    future.
5.  new nice high quality screencast

1.  docs should have narrative

6.  authentication for nbviewer as ipython org to get higher api call
    rate
7.  keyword search ?

Worksheets
----------

1.  Retire from notebook format next time we make changes.
2.  Create implied hierarchy in notebook UI that allows managing the
    notebook at the hierarchical level.
3.  Make sure that the state of the notebook view is persisted in the
    notebook metadata.
4.  Propagate heading cell metadata to its implied children.

Community
---------

1.  Resources

1.  ipython-dev
2.  ipython-user
3.  StackOverflow
4.  GitHub issues
5.  GitHub wiki
6.  HipChat - will replace IRC, but also cut into some of the traffic on
    ipython-user/ipython-dev.
7.  IRC - phase out
8.  Twitter @IPythonDev
9.  Google Plus

2.  Remove FAQ and transition those questions to StackOverflow.
3.  Create community page on ipython.org that lists our resources and
    when they should be used.

1.  why we have each resource and what we use it for

4.  Sidebar

1.  Remove IRC
2.  Add HipChat
3.  Add StackOverflow

5.  IPython Notebook/Qt Help

1.  Report a bug - point to GitHub wiki page describing how they can
    lookup and submit a bug report.
2.  Add HipChat link

6.  Min’s idea = consolidate ipython-user and ipython-dev into one list.

1.  Don’t close ipython-user, but deprecate it.
2.  Keep the archives, but close it down.
3.  Encourage people to use ipython-dev for everything.
4.  Remove ipython-user from sidebar/community list.

7.  Twitter: Min should share credentials with us. √
8.  Let’s start to post more updates to our News section. We will tweet
    and blog on our personal accounts to drive traffic.
9.  Screencasts.
10. Pauls thoughts on transitions away from IRC and ipython-user. Don’t
    shut them down, but tell people we (core devs) won’t be hanging out
    there as much. Strictly for the user community.
11. [http://www.discourse.org/](http://www.discourse.org/) (see
    [http://www.wired.com/wiredenterprise/2013/02/discourse/](http://www.wired.com/wiredenterprise/2013/02/discourse/)
    for more)

Core
----

4.  Input transformers

1.  Stateful input transformers, PR \#2447 - Fernando to review again
2.  API cleanups to be done - Thomas

Testing/CI
----------

1.  Shining Panda

1.  Fernando logon and pay a small amount
2.  More frequent Windows builds

2.  Travis

1.  For every pull request (without parallel)

3.  Documentation builds

1.  Shining Panda (every week)
2.  ReadTheDocs (every day)

JS tools / refactor

1.  currently: IPython css/less/js top-level adjacent to 3rd party
    packages
2.  we should treat IPython the same as third-parties (static/ipython
    next to static/jquery, etc.)
3.  how do we organize inside static/ipython?
4.  use bower
5.  new structure:

1.  static/ipython/notebook/\*.js
2.  static/ipython/notebook/css/\*.css
3.  static/ipython/base/\*.js
4.  etc.

6.  Brian will reorganize to above tree structure first
7.  Next, move to require.js
8.  later: split/cleanup css, less
9.  look at bootstrap

Docs

1.  Dev docs = GitHub Wiki
2.  Sphinx docs are for users
3.  progress on cleanup / purge in sphinx docs
4.  /examples on nbviewer
5.  new docs in examples/notebooks
6.  Paul’s job: think about better organization for docs

1.  propose an outline of the docs we should have

7.  leaves of the tree should be notebooks
8.  Cookbook repo

JS Security

1.  Single-user case

1.  JS can run arbitrary code as you (rm -rf $HOME)
2.  if authenticated, can do things in authenticated context (GitHub,
    etc.)

2.  Vulnerabilities

1.  markdown cells (on render / load)
2.  output

1.  $().html(output) for most output types, can result in script tags

3.  data-uri

1.  <img data-uri=”...”\> can include script tags, hover, onclick, etc.

4.  href, event attrs (onclick, etc.)
5.  pager display html, and potentially other rich format.

3.  Actions

1.  re-enable sanitization of MD
2.  sanitize output before $.html()
3.  sanitize encoded (not decoded) b64 data-uris
4.  deal with href / events?
5.  Why are tables stripped in pagedown (seems to be layout)?
6.  investigate sanitization for \*security-only\*.

4.  Future answer to restoring lost functionality:

1.  Our only answer will be custom.js in 1.0
2.  JS Plugins (server-side only at first)
3.  add js or plugin info to metadata with ‘do you trust...’ dialog.

Not yet discussed
=================

1.  Notebook

1.  toolset changes (bootstrap/less, requirejs, ACE?)
2.  Organization of js/css files.
3.  General css cleanup.
4.  JS Plugins
5.  JS Security
6.  Autosave (specific implementation is contentious, but a webapp
    without autosave is simply not acceptable, so we need to pick
    something). This may want to follow the Incremental Save
    implementation below.
7.  Persistent URLs
8.  Multi-User notebook server
9.  Incremental Save (significant frontend JS)
10. Collaboration (requires Incremental Save)
11. Interactive widgets
12. Raw input
13. Testing
14. State of full mathjax support (x-referencing, labels, etc).
15. Autosave / swap file
16. Filesystem navigation
17. Editing other file types
18. Emacs/vim keybinding modes for cells.
19. Bug: overwriting on renames.
20. Dashboard to browse filesystem ?
21. Bug: the scrollbar is disappearing when the pager opens up.
    Furthermore, the pager has no scroll bar. and is not monospace font.
22. SymPy printing extension broken?
23. Pager adds page complexity. Think of an alternative
24. Focus click bug

2.  Kernels

1.  non-Python kernels (R, Ruby, Julia?)
2.  Split KernelManager into distinct communication / process control
    entities (IPEP draft pending).

3.  Message Spec

1.  JSON publication (related to js plugins and security)
2.  remove pyout, and put prompt info in display\_data
3.  rename python-specific \`pyin\` (and \`pyout\` if it is not removed)
4.  consider single message displaying multiple objects (e.g. displaying
    list of images)
5.  image size information (possibly in metadata to avoid compatibility
    breakage)
6.  payload ‘source’ should probably be ‘handler’, and not reflect the
    location of its Python definition.

4.  Core

1.  R magics / integration seem to need some work
2.  IPEP 2 / PR \#2447 (input transformations)
3.  Improving startup time? We've been having pretty serious creep on
    this front...

5.  Docs

1.  We should rewrite \~everything
2.  100% notebooks for narrative docs
3.  Use Notebooks as source files for sphinx docs
4.  raw sphinx/rst only for API reference (not just autogen)
5.  Cleanup GitHub wiki
6.  Install instructions: point people to single-step installers for the
    core stack (EPD, Anaconda, PythonXY) on all common platforms.
7.  Docs as notebooks? How far to take this?
8.  Documenting the notebook format as of v3^[[c]](#cmnt3)^.
9.  Restructuring of docs with clear introductory guides.
10. Intro Screencasts?
11. Build website with visible examples so we can point people to them.
12. If you want, IPython 0.12.1 for Maemo and full IPython 0.13.1 for
13. MeeGo Harmattan could be cited somewhere (in FAQ, Wiki, etc, of
    IPython.org).
14. Links

1.  IPython for Maemo (with 100-200 thousand downloads)
2.  [http://talk.maemo.org/showthread.php?p=1168357](http://talk.maemo.org/showthread.php?p=1168357)
3.  IPython for MeeGo Harmattan (with estimated some thousand downloads)
    :
    [http://talk.maemo.org/showthread.php?t=79997](http://talk.maemo.org/showthread.php?t=79997)
4.  PyCon + PyData (March 2013)
5.  Tutorial Materials
6.  Sprint topics?

- No dev version of any dependencies! Jinja dev (on python 3 ?) now?

- When we run scripts in background threads (with --bg), where is the
job

object stored? Should return that...

- nbviewer display of local files (with files/ url handler)?

Statistics work
===============

Requirements and ideas

1.  Link to util.io/mathjax in docs
2.  Look into equation numbering, labeling and x-refer in
    mathjax/markdown
3.  Generation of static PDF slideshows, preferably via beamer export.
4.  Bibliographic support: perhaps use the {{bib[‘key’]}} idea? Work on
    the literate programming PR to polish this.
5.  Anchors for cross-notebook referencing.
6.  Homework assignment generation.
7.  Commenting on notebooks.

Stanford Neuroscience Grant
===========================

Main grant goals

1.  Install neuroscience db on a number of different systems to enable
    collaboration.
2.  Allow IPython Notebooks to be used for reproducible research and
    collaboration.
3.  Sharing data across sites is difficult because of authentication and
    access.
4.  Two year grant, starting in summer.
5.  Original proposal would have hired 2 professional software
    developers.
6.  Notebook sharing patterns:

1.  Main repository of notebooks that users checkout and apply to
    different data sets.
2.  User generated notebooks that are shared to other users.

Questions

1.  Total number of users? At each site? 100s per site, organized into
    groups.
2.  Independent notebook servers at each site? Yes
3.  

[[a]](#cmnt_ref1)Paul Ivanov:

"be bullish on this" - fdo

[[b]](#cmnt_ref2)Paul Ivanov:

Jason Grout seriously considering switching Sage notebook to use IPython
exclusively

[[c]](#cmnt_ref3)Matthias Bussonnier:

FInish the Validator ?