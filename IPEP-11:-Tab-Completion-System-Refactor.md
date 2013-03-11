### Abstract
The IPython tab completion machinery is messy, complicated, and difficult to extend. The IPython pull request [2701](https://github.com/ipython/ipython/pull/2701), for instance, envisioned a feature -- tab completion based on function-specific annotations -- which, because of the current sub-optimal completion API, required a quite a bit of hacking into the core completer logic to execute. This proposal envisions a refactoring of the IPython tab completion machinery -- largely `IPython/core/completer.py`, `IPython/core/completerlib.py`, and a little bit of `IPython/core/interactiveshell.py` -- in a way that simplifies and unifies the codebase while providing a powerful and flexible public-facing API, enabling its extensibility in 3rd party projects.

### History of the Tab Completion Machinery

- Started closely tied to GNU readline, and to the stdlib's `rlcompleter.py` module
- Hacked to be able to work with non-readline frontends, like the notebook and qtconsole.

### Current State of the Tab Completion Machinery
The completer machinery is architected as follows: there are a variety (roughly 1 dozen) of "matchers" or "completers", each of which, in essence, is supplied with a string containing the current state of the input line and responds with a list of possible matches. When the tab event is triggered, some or all of these completers are called, and the lists they produce may or may not be merged together. Regardless, a "master list" of completions is produced -- it may correspond to the results from simply one completer or to a merged list -- which is then supplied to either GNU readline (terminal frontend), or to the frontend itself (zmq frontends) for display to the user.

Two separate categories of matchers/completers exist. The first category is a set of "core matchers", declared on [lines 505-510](https://github.com/ipython/ipython/blob/master/IPython/core/completer.py#L505) of `IPython/core/completer.py` that are methods on the `IPython.core.completer.IPCompleter` class. These "core matchers" correspond to and generalize the python stdlib's `rlcomplete` mechanism. The second category of matchers, the "custom completers", interacts with the core system via a limited API. These completers are triggered by `IPython.core.completer.Completer.dispatch_custom_completer`. Each of the custom completers is registered to respond only to a specific string that must form the beginning of the input line. For instance, the `magic_run_completer` (declared [here](https://github.com/ipython/ipython/blob/master/IPython/core/completerlib.py#L246)) is registered only to be called when the input line starts with the string '%run'. If the line starts with anything else, e.g. `a = %run`, the completer is not executed. The decision about which custom completers are executed on a given input string is made a "string dispatcher" (instance of `IPython.utils.strdispatch.StrDispatch`) which is _dynamically_ injected into the `Completer` class by `IPython.core.interactiveshell.InteractiveShell` [here](https://github.com/ipython/ipython/blob/master/IPython/core/interactiveshell.py#L1957).

On a tab completion event, the custom completers are executed first, one at a time. If any of them successfully returns, its results exclusively constitute the "master list" that is supplied to the frontend. If none of the "custom completers" sucessfully match on the input string, the "core matchers" are executed. Depending on the status of a `Configurable` [option](https://github.com/ipython/ipython/blob/master/IPython/core/completer.py#L403), these results may either be merged together or not. The default and most widely used option is that they _are_ merged. In this code path, all five of the core matchers are executed, their results merged & sent to the frontend for display.

There is a convenient public facing API for the registration of new custom completers, which is used by some external projects including h5py.

### Limitations of the Current System
Because of the way the string dispatcher controls the execution of the custom completers, they have limited flexibility compared to the core matchers. When they are registered, they must declare exactly what initial substring or regular expression they respond to. This makes them convenient for tasks like special tab completion for specific magics, but makes them unsuitable for more complex tasks. For instance, the tab completion of keyword arguments, (e.g. `In [1]: numpy.loadtxt(del<TAB>`), which uses the signature of the last open callable to recommend keyword arguments, cannot be implemented using the custom completer mechanism and instead needs to be a "core matcher".

Edit: Acutally, the above is not quite right. By making the regular expression quite general, the custom completer can infact respond to arbitrary input lines. However, the fact remains that the custom completers are always exclusive-- once one returns sucessfully, its results are immediately sent to the frontend and no merging of completion lists is performed. It is not possible to return to the user a merged completion list which incorporates matches that were generated either from two or more custom completers or from one custom completers and one or more of the core matchers.

Furthermore, the current system does not provide the rich completion API that non-readline frontend could utilize. For instance, the [autocomplete.js](http://engineering.twitter.com/2013/02/twitter-typeaheadjs-you-autocomplete-me.html) library provides the ability to display autocompletion matches with contextual information -- information that our completion code cannot currently provide. In particular, for rich frontends, we'd like to provide the 'kind' of each match.

### Proposal :: Overview
This proposal envisions changes to the three majors aspects of the completion system. First, the two seperate groups of existing completers will be merged into one common framework. A single API will be provided that all of the matchers can interact with. Second, in addition to being used internally to organize the existing matchers, this API will be exposed publicly for third party modules. Third, the interaction between the completion system and the frontend will be richened, to provide type information on each match for display to rich frontends.

The `merge_completions` configurable will be removed, and instead the completers themselves will register themselves as exclusive or non-exclusive. (The `merge_completions` configurable really doesn't make much sense, because it relies on the rather arbitrary ordering of the current core matchers.)

### Proposal :: Changes to `IPython.core.completer`
The current `Completer` and `IPCompleter` classes will be removed, and replaced with a new class, `CompletionManager`. The `CompletionManager` orchestrates and controls the dispatch of the individual completers available in IPython. It provides the API for registering new completers as well.

The `CompletionManager` will look something like this:

```
class CompletionManager(Configurable):
    def register_completer(self, completer):
        """Register a new completer
        """
        pass

    def complete(self, block, cursor_position=None):
        """Recommend possible completions for a given input block
        
        Parameters
        ----------
        block : str
            Either a line of text or an entire block of text (e.g. a cell)
            providing the current completion context.
        cursor_position : int, optional
            The position of the cursor within the line, at the time that
            the completion key was activated. If not supplied, the cursor
            will be assumed to have been at the end of the line.
       
        Returns
        -------
        completions : dict, {str -> list(str)}
            The keys of the completions dict are the 'kind' of the completion,
            which may be displayed in rich frontends. Example 'kinds' might be,
            but are not limited to 'file', 'directory', 'object', 'keyword argument'.
        """
        # preprocess line to create a CompletionEvent
        # call all of the matchers
        # merge their results, and for each merged set of completions, listify and sort
        # it before returning it.
        pass
```

### Proposal :: `Matcher` API
Each kind of completion, such as files, directories, objects and object attributes, will
generally be computed by a separate matcher, as in the current system. However, instead
of these matchers being methods on a single monolithic class, they will each be a class
of their own, whose dispatch is controlled by the `CompletionManager`.

On the recommendation of Fernando, and following the example set by the magics system, there
will be two ways, from a user perspective, of registering new matchers. The first will
follow an object oriented design. The second will be a functional wrapper over this OO system.
Using the OO matchers system, the user will define a subclass of the to-be-provided `BaseMatcher`
abstract base class. The key method of this class, `match(self, event)` will take as input a
preprocessed version of the current line, and will return zero or more matches. This object will
then need to be registered with the `CompletionManager`. The functional wrapper over this system
will provide a function decorator that can be used to dynamically define a class with the supplied
function as the `match` method, and register it.

Here is a sketch of the `BaseMatcher`, which each matcher will extend:

```
class BaseMatcher(object):
    __metaclass__ = abc.ABCMeta

    def exclusive(self):
        """Should the completions returned by this matcher be the *exclusive* matches
        displayed to the user?

        If this property is true, other when this matcher returns sucesfully, its results will be
        shown exclusively to the client. Other matches will be excluded. This is generally not
        required, but may be suitable for use in contexts in which highly specialized
        inputs are required by the user, and other inputs may be invalid.
        """
        return False
    
    @abc.abstractmethod
    def match(self, event):
        """Recommend matches for a tab-completion event
        
        Parameters
        ----------
        event : CompletionEvent
            The event contains information about the context, including
            the current line, etc.
        
        Returns
        -------
        completions : dict, {str -> set(str)}
            The returned completions shall be a dict, mapping the 'kind' of the completion
            to a set of the recommend strings.
        """
        pass
```

Each matcher is passed an `Event` instance, which encapsulates the state of the line and any preprocessing
done. To the extent that there is common preprocessing done on the line by multiple matchers, it is
desirable to only do that computation once. For this reason, the `CompletionEvent` will contain a
`tokens` property, which will return the results of running the python stdlib's tokenizer on the input line.
If no matchers request this information, it will not be computed. On the other hand, if many matchers request this information, it will not be recomputed, since the `CompletionEvent` will be able to cache the tokens internally.

```
class CompletionEvent(object):
    """Container for information about a tab completion event
    
    Attributes
    ----------
    block : str
        The complete input block, upto the cursor position.
    lines : list
        The block, after splitting on newlines.
    split : list
        A list of strings, formed by splitting `block` on all readline delimiters.
    text : str
        The last element in `split`. For readline clients, all matches are expected
        to start with `text`.
    
    Properties
    ----------
    tokens : list
        A list of tokens formed by running the python tokenizer on `line`.
    """
```

### Proposal :: Changes to Messaging Protocol
In order to provide the 'kind' information about each of the completions to rich frontends, the messaging protocol needs to be extended. The current messaging protocol for completions is described here: http://ipython.org/ipython-doc/dev/development/messaging.html#complete. The `complete_reply` message type
will need to be extended to return not just a list of matches, but a dict mapping strings ('kind') to lists of strings (the matches). This follows from the return type of `CompletionManager.complete()`.

Furthermore, the specification of the `complete_request` message type needs to change, in order to really maintain kernel and language agnosticism in the frontend. By providing `text` and `line` in addition to `block`, the current messaging spec requires frontends to split the cell on newlines and, problematically, to split the last line on a set of python delimiters, such that `text` is assigned to be the last element of the line after splitting on these delimiters. This is problematic because it leaks python semantics into the frontend. A native Ruby, R, or Matlab kernel might use different delimiters (e.g. the dollar sign in R), and it should not be the place of the frontend to perform preprocessing on the block of text which is actually language specific and really best left for the kernel.

### Shim between old and new custom completer API
There is currently code in the wild in external 3rd party libraries that relies on the "custom completer" API. Thus, a shim should be created that allows that code to interact with the new system with no change. The documentation on the "custom completer" API will be changed, however, to reflect the new API and the shim will not be advertised as the recommended way to interact with the tab completion system. Because the old API does not contain a "kind" field for each match, some default must be chosen (e.g. "match") or something.

### Potential Implementation Issues
- Careful attention needs to be paid to properly dealing with filenames, especially those containing spaces on both windows and *nix. There are a number of subtleties here. The solution is extensive unit testing.
- Many of the completers involve interaction with the filesystem (`%cd` completer, etc), so care should be taken to avoid code duplication.
- Error handling within the matchers is a pain. Currently, the matchers are run within a `try matcher(); except: pass` block to ensure that a misbehaving mather doesn't negatively impact the user experience. But this negatively impacts the development workflow, making it difficult to track down bugs. Instead, perhaps this behavior can be set by some kind of a `debug` configurable that will default to False.