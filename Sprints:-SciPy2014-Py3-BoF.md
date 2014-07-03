Scheduled: Tuesday July 8th, 5.30pm, Grand Ballroom

Nick Coghlan, a core Python developer who has [thought a lot](http://python-notes.curiousefficiency.org/en/latest/python3/) about the Python 3 transition, will be with us.

Everyone is welcome

## Ideas for discussion

- Python 3.5 will have a [matrix multiplication operator](http://legacy.python.org/dev/peps/pep-0465/) (@).
- What other near-term changes to the interpreter do we want? Given that `@` will already make 3.5 particularly attractive to numerical users, 3.5 is a great target for other numerically-useful changes. Some potential items:
  - Support for [temporary elision](http://thread.gmane.org/gmane.comp.python.devel/148001/focus=148132). Python-dev seems at least potentially open to this, so next TODO item is probably to experiment with prototyping some implementations in CPython -- there are a few ideas in that thread -- and report back if any seem actually workable.
  - Memory tracing support for third-party allocators (some notes here: https://github.com/numpy/numpy/issues/4663). We already got support for `calloc` into 3.5, which was the biggest blocker, but it would be nice to have an API like the one sketched out in that bug for future-proofing (to avoid lockin) and for allowing the same tracing infrastructure to also work for other kinds of allocators like GPU allocators, aligned allocators, mmap, etc. Next TODO item is: talk to the CPython developers about this
  - Overloadable chained comparisons, to make `a < b < c` work on ndarrays. The hiccup here is that to check for overload methods, you have to examine the objects involved, but currently, chained comparisons are short-circuiting (if `a < b` is false, `c` is never evaluated), so we can't check whether `c` has any overload methods. My (njs's) feeling is that we should just write a PEP for overloading that makes chained comparisons *not* overloading, because seriously who writes `a < b < side_effecting_function()`, or even knows how that expression is currently evaluated. Next TODO item is: run an AST matcher over PyPI or something to get some statistics on whether people actually put function calls into the `c` slot. I have a copy of PyPI I downloaded to check against, but haven't written the script.

  - [Keyword arguments when indexing](https://github.com/stefanoborini/pep-keyword/blob/master/PEP-XXX.txt)? That is, support for `a[10, foo=20]`. This could allow syntax like `gridValues[x=3, y=5, z=0:8] = 0` instead of `gridValues.set_items(dict(x=3, y=5, z=slice(0, 8)), 0)`. [Start of discussion thread on python-ideas](https://mail.python.org/pipermail/python-ideas/2014-July/028250.html).

  - ...any other ideas? Add them!


- How to use function annotations?
  - [mypy](http://www.mypy-lang.org/) experimented with optional static typing; integrate that with some kind of compilation?
- Code blocks
  - Nick is the author of [PEP 3150](http://legacy.python.org/dev/peps/pep-3150/) and [PEP 403](http://legacy.python.org/dev/peps/pep-0403/), two possible new syntaxes for this issue.
  - This is a big topic - we should be careful not to spend the whole BoF talking about it.
  - A [recent post](http://stupidpythonideas.blogspot.com/2014/06/why-python-or-any-decent-language.html) arguing *against* the need for blocks in Python.
- Teaching Python 3
  - All of our core Scipy Stack packages support Python 3, though more specific tools still may not.
  - Software Carpentry is [discussing](https://github.com/swcarpentry/bc/issues/107) how and when to switch to Python 3 for their workshops.
  - Some university courses (e.g. [Ethan White's](http://programmingforbiologists.org/)) are starting to switch
  - Teaching materials supporting 2&3, or maintain separate copies of materials?
  - Some students will have to go back to Python 2, because of existing code in their lab, or libraries that they need. How best to prepare them for this?
- How do we get people to switch?
- How can we reduce the cost of switching, in an environment where any form of software maintenance remains hard to justify economically? Particularly tough aspects in science are the changed division semantics and the incompatible C API. Can we develop tools to help with them?
- Features we need in the scientific Python stack?
  - Issue of [numpy bytestring vs. unicode arrays](http://mail.scipy.org/pipermail/numpy-discussion/2014-January/068622.html) in Python 3.

This is a wiki page; please add your questions and ideas here.