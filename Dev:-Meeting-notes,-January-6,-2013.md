## IPython Overview

*   Core IPython experience
    -   Special syntax
    -   Kernel execution loop
*   Parallel
    -   Rewrite Scheduler, allow sharding
    -   Client caching
    -   PEP-3148
*   Frontends
    -   Terminal and console
    -   Qt
    -   Notebook
    -   Web console: future
*   Message Protocol
*   Notebook format^[[a]](#cmnt1)^
    -   nbconvert
    -   nbformat
*   Documentation -\> Paul
    -   Narrative docs (100% notebooks)
    -   Separate API docs
    -   JS into API docs (YUI doc + Sphinx)
*   Marketing/Outreach
    -   Stickers! mugs, posters, t-shirts
    -   Communicating with the public (twitter, google plus, project blog,
        wiki)
    -   PyCon booth
*   Third party projects:
    -   emacs notebook client
    -   vim client
    -   MS visual studio python tools
    -   IdleX
    -   Spyder
    -   Ninja IDE (as plugin)
    -   Sage^[[b]](#cmnt2)^
    -   Sympy
*   nbviewer
    -   link in the notebook
    -   nbconvert webservice
*   testing
    -   JS scripting?

##General Topics

*   Release/planning cycle
    -   6 Months release cycle
    -   Release happens before next planning meeting
    -   Planning meeting to plan work for next release
    -   Next release will be * 0!
    -   Tentatively July 15th release for * 0.
    -   Developer starts M-F July 15-19th.
*   Communicating release/planning
    -   Wiki page for each release detailing what we will do.
    -   Use GitHub milestones
*   Separate IPython into multiple repositories
    -   Thinking about it for the future, maybe a few releases away.
    -   Frontends, parallel, kernel?.
*   GitHub issue workflow for feature requests
    -   Use wishlist milestone for issues that have not been promoted to an
        active status.
    -   By default, anytime an issue is opened, the core developers target
        the issue to an active milestone or wishlist.
    -   Wishlist milestones should not be closed - if they are they should
        get milestone None.
*   Write contributing.md for GitHub
*   HipChat or Campfire?
    -   Much better experience than IRC.
    -   Costs money though for “persistent people”.
    -   Post to ipython-dev/ipython-user announcing our plans.
    -   Getting paid account is like getting commit rights - based on
        involvement.
    -   Post link to guest accounts on main ipython webpage.
    -   remove IRC from main ipython page at the same time.
*   Fernando will get back into
    [ipython@gmail.com](mailto:ipython@gmail.com) and we will publicize
    that as the project contact address. We will also use this for
    project accounts on things like AWS. He will send the info to the
    core devs.

## Notebook format

*   nbconvert (Fernando, Matthias)
    -   converters
        *   markdown: redo the markdown converter.
        *   pure html:
        *   pure latex: with different styles.
        *   Sphinx Friendly reST.
        *   reveal.js
        *   blogging engines (optional).
    -   Jinja branch
        *   Transforms are applied to notebook before conversion.
        *   Filters are applied to cells.
        *   Need to look at converted output to check it.
        *   Write test
        *   Write documentation
    -   Damian’s reveal branch
        *   Merge into nbconvert master.
        *   Matthias will rebase and continue in his Jinja2 branch.
    -   Proper package structure.
    -   setup.py scripts
        *   move to the merge as fast as possible, so it becomes a part of
            ipython’s setup.py
        *   no files outside of nbconvert/ directory
    -   Integration
        *   Put into IPython proper
        *   Integrate into Notebook web service and Notebook UI
*   nbformat
    -   Prompt number should only be empty or a number (web UI artifact).
    -   Create wiki page for proposed notebook format changes.
        *   Change attribute name from input-\>content for all cells.
        *   Remove worksheets.
    -   allow the user to easily save as an older version (previous format)
        from the Notebook UI (save as v3, save as v2)
    -   title <-\> filename discrepancy discussion (keep things as is,
        document it)
    -   Notebook metadata that specifies the kernel that should be used.
*   nbviewer (Matthias)
    -   url scheme (nbviewer.org/username/ gist of people’s ipynb files)
    -   nbviewer.ipython.org should stay, or at least be a redirect
    -   nbviewer roadmap on the wiki
    -   notebook “dummy” that’s half functional? let’s leave it to the
        future.
    -   new nice high quality screencast
        *   docs should have narrative
    -   authentication for nbviewer as ipython org to get higher api call
        rate
    -   keyword search ?

## Worksheets

*   Retire from notebook format next time we make changes.
*   Create implied hierarchy in notebook UI that allows managing the
    notebook at the hierarchical level.
*   Make sure that the state of the notebook view is persisted in the
    notebook metadata.
*   Propagate heading cell metadata to its implied children.

## Community

*   Resources
    -   ipython-dev
    -   ipython-user
    -   StackOverflow
    -   GitHub issues
    -   GitHub wiki
    -   HipChat - will replace IRC, but also cut into some of the traffic on
        ipython-user/ipython-dev.
    -   IRC - phase out
    -   Twitter @IPythonDev
    -   Google Plus
*   Remove FAQ and transition those questions to StackOverflow.
*   Create community page on ipython.org that lists our resources and
    when they should be used.
    -   why we have each resource and what we use it for
*   Sidebar
    -   Remove IRC
    -   Add HipChat
    -   Add StackOverflow
*   IPython Notebook/Qt Help
    -   Report a bug - point to GitHub wiki page describing how they can
        lookup and submit a bug report.
    -   Add HipChat link
*   Min’s idea = consolidate ipython-user and ipython-dev into one list.
    -   Don’t close ipython-user, but deprecate it.
    -   Keep the archives, but close it down.
    -   Encourage people to use ipython-dev for everything.
    -   Remove ipython-user from sidebar/community list.
*   Twitter: Min should share credentials with us. √
*   Let’s start to post more updates to our News section. We will tweet
    and blog on our personal accounts to drive traffic.
*   Screencasts.
*   Pauls thoughts on transitions away from IRC and ipython-user. Don’t
    shut them down, but tell people we (core devs) won’t be hanging out
    there as much. Strictly for the user community.
*   [http://www.discourse.org/](http://www.discourse.org/)
    
## Core

*   Input transformers
*   Stateful input transformers, PR \#2447 - Fernando to review again
*   API cleanups to be done - Thomas

## Testing/CI

*   Shining Panda
    -   Fernando logon and pay a small amount
    -   More frequent Windows builds
*   Travis
    -   For every pull request (without parallel)
*   Documentation builds
    -   Shining Panda (every week)
    -   ReadTheDocs (every day)

## JS tools / refactor

*   currently: IPython css/less/js top-level adjacent to 3rd party
    packages
*   we should treat IPython the same as third-parties (static/ipython
    next to static/jquery, etc.)
*   how do we organize inside static/ipython?
*   use bower
*   new structure:
    -   static/ipython/notebook/\*.js
    -   static/ipython/notebook/css/\*.css
    -   static/ipython/base/\*.js
    -   etc.
*   Brian will reorganize to above tree structure first
*   Next, move to require.js
*   later: split/cleanup css, less
*   look at bootstrap

## Docs

*   Dev docs = GitHub Wiki
*   Sphinx docs are for users
*   progress on cleanup / purge in sphinx docs
*   /examples on nbviewer
*   new docs in examples/notebooks
*   Paul’s job: think about better organization for docs
    -   propose an outline of the docs we should have
*   leaves of the tree should be notebooks
*   Cookbook repo

# JS Security

*   Single-user case
    -   JS can run arbitrary code as you (rm -rf $HOME)
    -   if authenticated, can do things in authenticated context (GitHub,
        etc.)
*   Vulnerabilities
    -   markdown cells (on render / load)
    -   output
        *   $().html(output) for most output types, can result in script tags
    -   data-uri
        *   <img data-uri=”...”\> can include script tags, hover, onclick, etc.
    -   href, event attrs (onclick, etc.)
    -   pager display html, and potentially other rich format.
*   Actions
    -   re-enable sanitization of MD
    -   sanitize output before $.html()
    -   sanitize encoded (not decoded) b64 data-uris
    -   deal with href / events?
    -   Why are tables stripped in pagedown (seems to be layout)?
    -   investigate sanitization for \*security-only\*.
*   Future answer to restoring lost functionality:
    -   Our only answer will be custom.js in * 0
    -   JS Plugins (server-side only at first)
    -   add js or plugin info to metadata with ‘do you trust...’ dialog.

