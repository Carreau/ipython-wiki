# Updates to completion and object_info messages

<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Min RK &lt;benjaminrk@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> November 13, 2013</td></tr>
<tr><td> Updated </td><td> November 13, 2013</td></tr>
</table>


`completion` and `object_info` are two messages with Python or IPython-specific remnants,
and we want to improve the situation for these messages with non-Python kernels.

The first problem is that when frontends don't know what language the kernel is in,
lexing becomes untenable.
This is especially challenging for object_info, where an actual object name must be found,
which can include symbols like `?`or `!` in some languages other than Python.

To address this, we will move lexing to the kernel for messages of this type.
Both requests will have two keys:

```python
{
    "text" : "string",  # the context of the request
    "cursor_pos" : int, # cursor position at the time of request
}
```

Where `text` is the text context, and `cursor_pos` is the integer offset of the cursor position.
In the most general case, `text` might be an entire cell in a notebook,
but it can also be just the line in a line-based interface.
The important thing here is that `text` *may* be a multiline string.

## completion messages

The full completion request/reply messages are:

`complete_request`:

```python
content = {

    # The (possibly multiline) string context of the completion request.
    # This is either the line or entire cell in which the completion is being requested.
    "text" : "multiline\nstring",

    # the cursor position, as an offset from the start of `text` when the user hit TAB.
    "cursor_pos" : int
}
```

For example, with the cell:

```python
a = 5
a.is<TAB>
```

the request content would be

```python
{
    "text" : "a = 5\na.is",
    "cursor_pos" : 10,
}
```

`complete_reply`:

```python
{
    # a list or dictionary of matches.
    # if a dictionary, keys are category strings, such as 'file' or 'attributes'.
    # It is up to the frontend to interpret the categories.
    # The simplest interpretation is just to flatten everything to a single list.
    'matches' : list_or_dict,

    # the substring of the matched text
    # this is typically the common prefix of the matches,
    # and the text that is already in the block that would be replaced by the full completion.
    # This would be 'a.is' in the above example.
    'matched_text' : str,

    # status should be 'ok' unless an exception was raised during the request,
    # in which case it should be 'error', along with the usual error message content
    # in other messages.
    'status' : 'ok',
}

### RFC

- [ ] should the 1.0-style list matches still be supported?
- [ ] should kernels be allowed to suggest category ordering?

## Introspection Messages

An `object_info_request` message now has the same content as `complete_request`:

`object_info_request`:

```python
content = {

    # The (possibly multiline) string context of the request.
    # This is either the active line or entire cell.
    "text" : "multiline\nstring",

    # The cursor position indicating the location of the object's identifier.
    # It is up to the kernel to parse out what symbol should be inspected.
    "cursor_pos" : int,

    # The level of detail desired.  The default (0) gets most information about the object.
    # 1 tries to add the source code of the object.
    # in IPython, level 0 is equivalend to 'x?', 1 is equivalent to 'x??'.
    'detail_level' : int,
```

**This is where we still need input from kernel authors**

The `object_info_reply` is not just Python specific, but highly *IPython*-specific.
We need feedback from non-Python kernel authors on this one,
either for missing information, or too-Python-specific information.

Some keys do not make sense for all kinds of objects,
especially when considering non-Python kernels.
While every key should be defined, any key may be left empty
if it does not make sense for the given object.
If it is ignored, it should be an appropriate type (e.g. empty string or null).

**Any value can be null, all should be defined.**


```python
content = {
    # The name of the object that was requested
    'name' : str,

    # Boolean flag indicating whether the named object was found or not.
    # If it's false, no fields below need be defined.
    'found' : bool,
    
    # detail level 0

    # is it an IPython magic or alias?
    # non-IPython kernels can always leave these as False
    'ismagic' : bool,
    'isalias' : bool,

    # The name of the namespace where the object was found
    # ('builtin', 'magics', 'alias', 'interactive', etc.)
    'namespace' : str,

    # The string form of the object, possibly truncated for length if
    # detail_level is 0
    'string_form' : str,

    # The type name will be type.__name__ for normal Python objects, but it
    # can also be a string like 'Magic function' or 'System alias'
    'type_name' : str,
    
    # The class of the object (obj.__class__ in Python, if defined).
    'base_class' : str,
    
    # For objects with a length defined, such as containers.
    'length' : int,
    
    # If the file in which an object is defined can be found,
    # we give its full path
    'file' : str,

    # For pure Python callable objects, we can reconstruct the object
    # definition line which provides its call signature.  For convenience this
    # is returned as a single 'definition' field, but below the raw parts that
    # compose it are also returned as the argspec field.
    'definition' : str,

    # The individual parts that together form the definition string.  Clients
    # with rich display capabilities may use this to provide a richer and more
    # precise representation of the definition line (e.g. by highlighting
    # arguments based on the user's cursor position).  For non-callable
    # objects, this field is empty.
    'argspec' : { 
        # The names of all the arguments
        args : list,
        # The name of the varargs (*args), if any
        varargs : str,
        # The name of the varkw (**kw), if any
        varkw : str,
        # The values (as strings) of all default arguments.  Note
        # that these must be matched *in reverse* with the 'args'
        # list above, since the first positional args have no default
        # value at all.
        defaults : list,
    },

    # For instances, provide the constructor signature (the definition of
    # the __init__ method):
    'init_definition' : str,

    # Docstrings: for any object (function, method, module, package) with a
    # docstring, we show it.  But in addition, we may provide additional
    # docstrings.  For example, for instances we will show the constructor
    # and class docstrings as well, if available.
    'docstring' : str,

    # For instances, provide the constructor and class docstrings
    'init_docstring' : str,
    'class_docstring' : str,

    # If it's a callable object whose call method has a separate docstring and
    # definition line:
    'call_def' : str,
    'call_docstring' : str,
    
    # detail_level 1

    # If detail_level was 1, we also try to find the source code that
    # defines the object, if possible.  The string 'None' will indicate
    # that no source was found.
    'source' : str,
}
```

### RFC 

Questions to external kernel authors:

- are any of these names bad fits for your language? Should we change things like `init`
  to `constructor`
- is there anything *missing* that is important to summarizing objects in your language?