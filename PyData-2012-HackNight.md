MULTI-USER NOTEBOOK & OTHER IPYTHON DISCUSSION AT PYDATA HACKNIGHT
------------------------------------------------------------------

**Note** this was originally [a gist](https://gist.github.com/ivanov/05d9bd382c9216f614e8), moved here for reference.
 
security discussion (I missed the beginning of this -pi)
 
jason chin: it's be nice to have multiple users for 
 
the notebook - dual-notebook discussion
 
david strauss: git has functionality to be file-level aware - and have it
ignore portions of a file
    - git config - clean and smudge filter mechanisms (can be file type aware)
 
brian: this is crazy! no one else does this (generate rich text, embedded videos
and html, generated plots, *AND* version control friendly)
 
"save stripped" for VCS-friendly notebooks  - can't be default - we need In[]
prompt numbers
 
separation between things the user 
 
there's an sqlite database for all sent code
 
Format we have we keep identical - and we create a new thing that is the
stripped version of it
 
brian: notebook save action has a hook system - that triggers things when it is
called (that maybe incorporated the git clean/smudge stuff)
 
fernando: pipeline maybe too much to swallow for "regular users"
 
chain of filters / different renderers - common options are available -
scripts, stripped (raw) scripts - code only, transformed ipython syntax or not,
version-control friendly of the notebook, ReST. others provided by exposing the
chain of filters functionality
 
 
fernando: github - getting meaningful diffs of notebooks on github (like the
image diff they already have, but for whole notebooks - with different types of
cells of the notebook properly rendered.
 
jason: teach git about cells - make each object a cell?
 
we can have an 'nbdiff' command  - your own little thing - which uses git under
the hood - or just compares two separate notebook files.  
 
john:  one way to do it would be just to do the diff on the exported clean py
files.
 
sharing of documents: - we should just use git and github for that
    - save on github and a share - gist and full github for full project (a
      directory with .ipynb and also data csv)
 
brian: don't want to create a git ui on the web - github has one:
 
paul: but, github web ui: should be able to make new files and folders in git
 
david: can have separate git driver for diff - can be "lossy" - not as precise
 
david: you want to have both - a local good representation for diffing locally
(don't want to have to push to github to get this), and a github friendly one -
for sharing and collaborative 
 
hook up "terminal" tab - or terminal cell type - only for the shell - they
don't get saved - and the `!` is prepended to everything that happens there.
this is a nice entry point for other people.
 
brian - slippery slope -  xcode has git integrated everywhere, will want more
functionality, full color on the diffs, etc.
 
fernando: have an upload magic - "put" so that in lab environments - they
don't have to scp - and then they can have their full screen codemirror tab,
too.  notebook could have a filesystem view so they can grab files from the
kernel using notebook.
 
brian: very easy to do pam authentication in python. but there's this company
in redmond.... punt on it.
 
brian: back to multi-user environment - a hotel/motel is not a single family
dwelling. the single user experience of the notebook would go away - don't
think it's possible to have both. (who will maintain it? you can't handle this
logic in subclassing) all urls : for each - go write get, post, etc. - for each
request I write a request in tornado, with each having a regular expression
that will have the things that it needs to have encoded in it.
 
fernando: job of applications is different - one is the hotel, the other is
just the hotel room. - start up a new single-user instance for each new
instance.  it's just so the user doesn't have to type at the prompt "ipython
notebook" - one http port per user advantages: 
    - simple, and 
    - preserves the local, application-like thing we've got right now.
    - and possibility of someone taken the current experience and reuse it for
      something else altogether.
what do we lose if we do this: 
    - the notebook itself won't we multi-user aware. 
    - can't do any sharing that isn't opaque to the notebook. 
    - can't add anything to the notebook ui
    - port management thing is a huge concern.
 
brian: implementation side: for the req handlers - reg ex groups capture the
parts you'd like to match - those get passed as arguments. If you subclassing -
the signature of your methods change turn off authentication / specify list of
users that can access it the url scheme will look more complicated
 
------
Names correspond to people who kicked off particular questions or points, not
everything that is stated is attributable to the name at the beginning of a
given snippet. Also putting this on Gist so that others can clarify and add
information that I missed or mis-captured.