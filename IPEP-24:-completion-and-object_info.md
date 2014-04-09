# Updates to completion and object_info messages

<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Min RK &lt;benjaminrk@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> November 13, 2013</td></tr>
<tr><td> Updated </td><td> April 8, 2014</td></tr>
<tr><td> Implementation </td><a href="https://github.com/ipython/ipython/pull/4536">4536</a> </td></table>
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
    "code" : "multiline\nstring",

    # The line number of the cursor.
    "lineno" : int,

    # The cursor position within the active line.
    "col" : int,
}
```

Where `code` is the code context, and The cursor's column and line number are specified as `col` and `lineno`.
In the most general case, `code` might be an entire cell in a notebook (or even the entire notebook),
but it can also be just the line in a line-based interface.
The important point here is that `code` *may* be a multiline string.

## completion messages

The full completion request/reply messages are:

`complete_request`:

```python
content = {

    # The (possibly multiline) string context of the completion request.
    # This is either the line or entire cell in which the completion is being requested.
    "code" : "multiline\nstring",

    # The line number of the cursor.
    "lineno" : int,

    # The cursor position within the active line.
    "col" : int,
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
    "code" : "a = 5\na.is",
    "col" : 10,
    "lineno" : 1,
}
```

`complete_reply`:

```python
{
    # A list of matches.
    # if a dictionary, keys are category strings, such as 'file' or 'attributes'.
    # The simplest interpretation is just to flatten everything to a single list.
    'matches' : list,

    # A list of extra information for matches.
    # Must be either empty or length equal to 'matches'.
    # It is up to the frontend to interpret these values.
    # If the frontend doesn't understand a given value, it has no effect.
    # In the simplest case, these are just strings describing the completion,
    # e.g. 'attribute' or 'file', which the frontend can use add some visual indicator.
    'matches_extra' : list,

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
```

### RFC

- [ ] feedback on matches_extra

## Introspection Messages

An `object_info_request` message now has the same content as `complete_request`:

`object_info_request`:

```python
content = {

    # The (possibly multiline) string context of the request.
    # This is either the active line or entire cell.
    "code" : "multiline\nstring",

    # The line number of the cursor.
    "lineno" : int,

    # The cursor position within the active line.
    "col" : int,

    # The level of detail desired.  The default (0) gets most information about the object.
    # 1 tries to add the source code of the object.
    # in IPython, level 0 is equivalend to 'x?', 1 is equivalent to 'x??'.
    "detail_level" : int,
```

**This is where we still need input from kernel authors**

The `object_info_reply` in 1.x was not just Python specific, but highly *IPython*-specific.
Since there has been little to no use of the detailed content of the info reply other than simply displaying it,
and given the complexity of proposing language-agnostic introspection fields,
we are deferring to the kernel for formatting and changing object_info_reply to
a mime-bundle message (like display_data).


```python
content = {
    "status" : "ok",
    # The name of the object whose info was requested
    "name" : str,

    # Boolean flag indicating whether the named object was found or not.
    # If it's false, no fields below need be defined.
    "found" : bool,
    
    # the data/metadata keys are interpreted the same as any mime-bundle message
    # (display_data, execute_result, etc.)
    # a mime-type keyed dictionary of representations of the data
    "data" : {"mime/type" : "data"},
    
    # a mime-type keyed dictionary of metadata
    "metadata" : {"mime/type" : "metadata"},
}
```

### RFC 

Questions to external kernel authors:

- [ ]: What *existing* functionality would be lost if structured info replies are removed?
