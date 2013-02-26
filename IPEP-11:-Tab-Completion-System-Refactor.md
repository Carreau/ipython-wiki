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
Because of the way the string dispatcher controls the execution of the custom completers, they have limited flexibility compared to the core matchers. When they are registered, they must declare exactly what initial string they respond to. This makes them convenient for tasks like special tab completion for magics, but makes them unsuitable for more complex tasks. For instance, the tab completion of keyword arguments, (e.g. `In [1]: numpy.loadtxt(del<TAB>`), which uses the signature of the last open callable to recommend keyword arguments, cannot be implemented using the custom completer mechanism and instead needs to be a "core matcher".

Edit: Acutally, the above is not quite right. The string dispatcher actually permits the custom completer to be registered with a regular expresssion instead of simply an exact string match. By making the regular expression quite general, the custom completer can infact respond to arbitrary input lines. However, the fact remains that the custom completers are always exclusive-- once one returns sucessfully, its results are immediately sent to the frontend and no merging of completion lists is performed. It is not possible to return to the user a merged completion list which incorporates matches that were generated either from two or more custom completers or from one custom completers and one or more of the core matchers.

Furthermore, the current system does not provide the rich completion API that non-readline frontend could utilize. For instance, the [autocomplete.js](http://engineering.twitter.com/2013/02/twitter-typeaheadjs-you-autocomplete-me.html) library provides the ability to display autocompletion matches with contextual information -- information that our completion code cannot currently provide.

### Proposal :: Overview
This proposal envisions changes to the three majors aspects of the completion system. First, the two seperate groups of existing completers will be merged into one common framework. A single API will be provided that all of the matchers can interact with. Second, in addition to being used internally to organize the existing matchers, this API will be exposed publicly for third party modules. Third, the interaction between the completion system and the frontend will be richened, to provide type information on each match for display to rich frontends.

The `merge_completions` configurable will be removed, and instead the completers themselves will register themselves as exclusive or non-exclusive. (The `merge_completions` configurable really doesn't make much sense, because it relies on the rather arbitrary ordering of the current core matchers.)

### Proposal :: More Specifics
- Scrap the `IPython.core.completer.Completer` class. This is just a copy of the readline completer that's in the stdlib's `rlcompleter.py`. It's not necessary that IPython's actual completer subclass that.
- Pay careful attention to the difference between splitting on RL delimiters and tokenizing. The behavior is different for things like string literals. For most purposes, the split on RL delimiters is sufficient. Tokenization using the stdlib's `tokenize` is more flexible though. Perhaps both should be provided in the `Event`.
- Every matcher will subclass a new `BaseMatcher` abstract base class.
- Both the individual matchers and the `Completer` class will return a list of dicts. Each dict shall contain the keys 'match' and 'type'. Later on, it is possible that this API could be extended to support additional metadata.

Here is a sketch of the `BaseMatcher`:
```
class BaseMatcher(object):
    __metaclass__ = abc.ABCMeta

    @abc.abstractproperty
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
        event : Event
            The event contains information about the context, including
            the current line, etc.
        
        Returns
        -------
        completions : list, None
            The returned completions shall be a list of dicts, containing the keys 'match',
            and 'type'. The value for 'match', a string, gives the matched text. The value
            for 'type', a string, gives information about the type of the object being
            suggested to the user, suitable for display by rich frontends. If this matcher fails
            to match any results, it shall return None.
        """
        pass
```

Each matcher is passed an `Event` instance, which encapsulates the state of the line and any preprocessing done.
```
class Event(object):
    """Container for information about a tab completion event
    
    Attributes
    ----------
    line : str
        The complete input line, to to the cursor position.
    split : list
        A list of strings, formed by splitting `line` on all readline delimiters.
    text : str
        The last element in split. For readline clients, all matches are expected
        to start with `text`.
    tokens : list
        A list of tokens formed by running the python tokenizer on `line`.
    """
```

Here is a sketch for the `Completer`, which is the replacement for `IPython.core.completer.IPCompleter`:
```
class Completer(object):
    def register_matcher(self, matcher):
        """Register a matcher (instance of `BaseMatcher`) for recommending tab-completion matches.
        """
        pass

    def complete(self, line, cursor_location, ...):
        """Find completions for the current line
        
        Parameters
        ----------
        line : str
            The current line
        cursor_location : int
            The index of the cursor in the line
      
        Returns
        -------
        completions : list
            The returned completions are a list of dicts, containing the keys 'match', and 'type'.
            The value for 'match', a string, gives the matched text. The value for 'type',
            a string, gives information about the type of the object being suggested to the user,
            suitable for display by rich frontends.
        """
        pass
```


GNU Readline clients can use a subclass of `Completer`:
```
class ReadlineCompleter(Completer):
    def rlcomplete(self, text, state):
         """Get the state-th possible completion for 'text'. 

         This is called successively with state == 0, 1, 2, ... 
         by GNU Readline clients until it returns None.
         The completion should begin with 'text'.
         """
         pass
```

### Questions
- Should the individual builtin matchers be `Configurable`?

### Potential Implementation Issues
- Careful attention needs to be paid to properly dealing with filenames, especially those containing spaces on both windows and *nix. There are a number of subtleties here.
- Many of the completers involve interaction with the filesystem (`%cd` completer, etc), so care should be taken to avoid code duplication.
- Error handling within the matchers is a pain. Currently, the matchers are run within a `try matcher(); except: pass` block to ensure that a misbehaving mather doesn't negatively impact the user experience. But this negatively impacts the development workflow, making it difficult to track down bugs. Instead, perhaps this behavior can be set by some kind of a `debug` configurable that will default to False.