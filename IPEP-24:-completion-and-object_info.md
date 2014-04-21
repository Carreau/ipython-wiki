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

    # The position of the cursor in the code block.
    "cursor_pos" : int,
}
```

Where `code` is the code context, and The cursor's position (in absolute unicode offset, counting from zero) is `cursor_pos`.
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

    # The position of the cursor in the code block.
    "cursor_pos" : int,
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
    "cursor_pos" : 10,
}
```

`complete_reply`:

```python
content = {
    # A list of string matches.
    "matches" : list,
    
    # the range to be replaced by 
    "cursor_start" : int,
    "cursor_end" : int,
    
    # kernels can add extra information to metadata,
    # for use by GUI extensions to affect the display of completions.
    "metadata" : dict,

    # status should be 'ok' unless an exception was raised during the request,
    # in which case it should be 'error', along with the usual error message content
    # in other messages.
    'status' : 'ok',
}
```

### RFC

- [ ] feedback on matches_extra

## Inspection Messages

`object_info_request/reply` will be renamed to `inspect_request/reply`.
An `inspect_request` message now has the same content as `complete_request`:

`inspect_request`:

```python
content = {

    # The (possibly multiline) string context of the request.
    # This is either the active line or entire cell.
    "code" : "multiline\nstring",

    # The position of the cursor in the code block.
    "cursor_pos" : int,

    # The level of detail desired.  The default (0) gets most information about the object.
    # 1 tries to add the source code of the object.
    # In IPython, level 0 is equivalend to 'x?', 1 is equivalent to 'x??'.
    "detail_level" : int,
}
```

**This is where we still need input from kernel authors**

The `object_info_reply` in 1.x was not just Python specific, but highly *IPython*-specific.
Since there has been little to no use of the detailed content of the info reply other than simply displaying it,
and given the complexity of proposing language-agnostic inspection fields,
we are deferring to the kernel for formatting and changing object_info_reply to
a mime-bundle message (like display_data).

`inspect_reply`:

```python
content = {
    # status should be 'ok' unless an exception was raised during the request,
    # or nothing was found to inspect,
    # in which case it should be 'error', along with the usual error message content
    # in other messages.
    "status" : "ok",
    
    # name is optional, and indicates the name or token that was inspected.
    "name" : str,
    
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
