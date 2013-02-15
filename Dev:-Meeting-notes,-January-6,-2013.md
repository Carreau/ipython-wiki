The core IPython developers met for a three day meeting at UC Berkeley. In attendance were: Fernando, Min,
Brian, Paul, Matthias and Thomas. This document is mostly unedited notes from that meeting.

## IPython Overview

We begin by identifying the main areas of the project:

*   Core IPython experience
    -   Special syntax (`%`, `!`, `?`, tab completion, aliases)
    -   Kernel/frontend architecture
*   Parallel
    -   Rewrite Scheduler, allow sharding
    -   Client caching
    -   PEP-3148
*   Frontends
    -   Terminal
    -   Console
    -   QtConsole
    -   Notebook
    -   Web console
*   Message Protocol
*   Notebook format
    -   nbconvert
    -   nbformat
*   Documentation
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
    -   Sage
    -   Sympy
*   nbviewer
    -   link in the notebook
    -   nbconvert webservice
*   testing
    -   JS scripting?

And main third party projects that build on top of IPython:

-   emacs notebook client
-   vim client
-   MS visual studio python tools
-   IdleX
-   Spyder
-   Ninja IDE (as plugin)
-   Sage^[[b]](#cmnt2)^
-   Sympy

##General Topics

*   Release/planning cycle
    -   6 Months release cycle
    -   Release happens before next planning meeting
    -   Planning meeting to plan work for next release
    -   Next release will be 1.0
    -   Tentatively July 15th release for 1.0
    -   Next developer meeting M-F July 15-19th.
*   Communicating release/planning
    -   Roadmap document will be produced at each meeting, hosted as a GitHub
        wiki page
    -   Use GitHub milestones to organize work by releases. See our GitHub workflow
        wiki page
*   Separate IPython into multiple repositories
    -   Thinking about it for the future, maybe a few releases away
    -   Only makes sense for components that are really stable
    -   Frontends, parallel, kernel?
*   GitHub issue workflow for feature requests
    -   Use wishlist milestone for issues that have not been promoted to an
        active status.
    -   By default, anytime an issue is opened, the core developers target
        the issue to an active milestone or wishlist.
    -   Wishlist milestones should not be closed - if they are they should
        get milestone None.
*   Write contributing.md for GitHub
* Transition away from IRC to HipChat
    -   We considered Campfire and HipChat, but the pricing model of HipChat is a
    better match for an open source projects as it allows unlimited free guests.
    -   We will not close down the #ipython IRC channel, but tell people IPython devs
    probably won't hang out there.    
    -   Much better experience than IRC.
    -   Post to ipython-dev/ipython-user announcing our plans.
    -   Getting paid account is like getting commit rights - based on
        involvement.
    -   Each paid person is only $1/month.
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
    -   Proper package structure
        *   We really need to create a proper package structure for nbconvert repo
        *   Will help organize code and make the transition to main repo easy
        *   No files outside the nbconvert package directory - it will just be copied
            into the main ipython repo.
    -   setup.py scripts
        *   We need to create one ASAP to make it easy to run nbconvert
    -   Integration
        *   Put into IPython proper
        *   Integrate into Notebook web service and Notebook UI
*   nbformat
    -   Prompt number should only be empty or a number (web UI artifact).
    -   Create wiki page for proposed notebook format changes.
        *   Change attribute name from "input" to "content" for all cells.
        *   Remove worksheets.
    -   Allow the user to easily save as an older version (previous format)
        from the Notebook UI (save as v3, save as v2)
    -   We discusssed the difference between the notebooks "title" and "filename".
        We are going to keep things as it is.
    -   Notebook metadata that specifies the kernel (python, ruby, etc.) that should be used.
*   nbviewer (Matthias)
    -   url scheme (nbviewer.org/username/ gist of people’s ipynb files)
    -   nbviewer.ipython.org should stay, or at least be a redirect
    -   nbviewer roadmap on the wiki
    -   notebook “dummy” that’s half functional? let’s leave it to the
        future.
    -   new nice high quality screencast
    -   authentication for nbviewer as ipython.org to get higher api call
        rate.
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
*   Overall, we want to consolidate our resources to simplify.
*   Strong agreement that we should move as many simple Q&A things to StackOverflow.
*   Remove FAQ and transition those questions to StackOverflow.
*   Create a "Community" page on ipython.org that lists our resources and
    when they should be used.
    -   why we have each resource and what we use it for
*   Sidebar
    -   Remove IRC
    -   Add HipChat
    -   Add StackOverflow
*   IPython Notebook/Qt Help
    -   Add a link to the GitHub wiki page about how users can/should Report a bug.
    -   Add HipChat link
*   Min’s idea = consolidate ipython-user and ipython-dev into one list.
    -   Don’t close ipython-user, but deprecate it.
    -   Keep the archives, but close it down.
    -   Encourage people to use ipython-dev for everything.
    -   Remove ipython-user from sidebar/community list.
    -   Everyone is on board with this, but we probably need more discussion about
        exactly how this will happen
    -   Paul said we should not actually close the IRC channel and ipython-user, but
        that we should just tell people we won't be hanging out there.
*   Twitter: Min will share credentials with us on HipChat
*   Let’s start to post more updates to our News section. We will tweet
    and blog on our personal accounts to drive traffic.
*   Screencasts
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

*   Currently IPython css/less/js in in the top-level `static/` adjacent to 3rd party
    packages. This makes it difficult to separate our code from others
*   We are going to use bower to install and manage 3rd party components in `static/`
*   We should move all IPython css/less/js to `static/ipython`
*   How do we organize inside `static/ipython`?
*   New structure:
    -   Subdirectories for each page or component (`base`, `notebook`, `dashboard`)
    -   `static/ipython/notebook/\*.js`
    -   `static/ipython/notebook/css/\*.css`
    -   `static/ipython/notebook/less/\*.less`
*   Brian will reorganize to above tree structure first
*   Next, move to require.js
*   Then we will split our css into multiple files to match our js files.
*   Finally start to make the transition to Bootstrap

## Docs

*   We currently have docs in a couple of different places
    -   Sphinx
        *  Narrative docs
        *  API docs
        *  Dev docs
    -   GitHub wiki
        *  Cookbook
        *  Random things
        *  More dev docs
*   We are going to move all of our dev docs to the GitHub wiki
*   Sphinx docs are for users and 3rd party developers
*   In the long run, all of our user focused narrative docs will be written using 
    notebooks, probably in a separate repo, and hosted on nbviewer.
*   For now, we are going to try and make progress in cleaning up and purging content
    in our Sphinx docs
*   Right now our `/examples` directory has a mix of narrative docs and examples. The
    narrative docs will be moved out of there and the true examples will be made viewable on nbviewer
*   Paul’s first task is to think about how our narrative documentation should be organized.
        -   Propose an outline of the docs we should have
        -   Leaves of the tree should be notebooks
*   We talked about creating a `cookbook` repo for user contributed notebook examples. It would
    also be nice to move our Cookbook content from the GitHub wiki to this repo as notebooks.

# JS Security

*   Single-user case
    -   JS can run arbitrary Python code as you (rm -rf $HOME)
    -   If you are authenticated, the JS can do things in authenticated context (GitHub,
        etc.)
    -   Very serious situation
*   Vulnerabilities
    1.  Markdown cells render and page load will run `<script>` tags
    2.  Most of our output is appended to the page by doing `$().html(output)`. We need
        to make sure this sanitizes all problematic tags, esp. `<script>`
    -   We inject images onto the page using `data-uri` which can embed `<script>` tags
        *   `<img data-uri=”...”\>`
        *   Need to clean the base64 encoded image data
    -   JS code can be embedded into tag event handlers such as onclick
    -   pager display html, and potentially other rich format
    -   MathJax may have vulnerabilities on this front as well
*   Actions
    -   Use Pagedown's Markdown sanitizer for our Markdown cells.
    -   Sanitize output before appending content using `$().html(output)`
    -   Sanitize encoded (not decoded) b64 data-uris
    -   Deal with JS embedded in tag event handlers
    -   Why are tables stripped in pagedown (seems to be layout)?
    -   investigate the minimum set of tag that need to be sanitized for \*security-only\*
    -   Contact folks at GitHub and MathJax about MathJax security issues see https://github.com/github/gollum/issues/288
*   Future answer to restoring lost functionality:
    -   Our only answer will be custom.js 1.0
    -   JS Plugins (server-side only at first)
    -   Add JS or plugin info to metadata with ‘do you trust...’ dialog.
