<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Min RK &lt;benjaminrk@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> April 6, 2013</td></tr>
<tr><td> Updated </td><td> April 28, 2013</td></tr>
<tr><td> Implementation </td><td> <a href="https://github.com/ipython/ipython/pull/3190">PR 3190</a> </td></table>


We've learned a lot in our first 18 months with the message spec,
and we got a remarkable amount right.
But we didn't get everything right, and we want to clean some things up prior to 1.0.

A few names need to change, because they are Python-specific, and our message spec is not.

## `pyin`

When an `execute_request` arrives, a `pyin` message is published with the code about to be run.
This should not be a Python-specific name.

- `pyin` should be renamed to something more generic,
  such as `input` or `code`, or `execute_content` or `execute_notification` or some such.


## `pyout`

`pyout` is also Python-specific, but has a more pressing issue - it is identical to `display_data`,
except with the addition of a `prompt_number` field. I propose *removing* `pyout`,
and adding the necessary information as a flag in `display_data`.
One possible implementation is to just add `prompt_number` to `display_data`,
where `prompt_number` is generally null, but has a value for current `pyout` cases.


## display metadata

The `display_data` message currently has a `metadata` field,
which is not yet used for anything.
We propose some basic usage guidelines for the metadata field,
and some associated changes to the Python API and notebook file format.

In general, there is metadata associated with each output.
This is the `metadata` key of the `display_data` message.
We propose the convention that sub-dicts of the metadata dict,
under the same mime-type keys as the `data` dictionary,
should be interpreted as metadata specific to that particular output.

In general, this will mean that rich frontends will want to implement logic similar
to the following to handle metadata of a particular output:

```python
data = message['content']['data']
metadata = message['content']['metadata']

handle_global_metadata(metadata)

for mimetype, mime_data in data.items():
    mime_md = metadata.get(mimetype)
    if mime_md:
        handle_mime_specific_metadata(md)
    handle_mime_output(mime_data)
```

In particular, the javascript handlers for each mimetype in the IPython Notebook now receive
both the data and the metadata for each individual mimetype, so

```javascript
OutputArea.prototype.append_html = function (html, element)
```

has become

```javascript
OutputArea.prototype.append_html = function (html, md, element)
```

Where `html` is the content of `data['text/html']`,
and `md` is the content of `metadata['text/html']`.


### Python API changes

- The `publish_foo` methods from `core.displaypub` are to be removed.
  The `display_foo` methods are our public API,
  and `display_foo(raw=True)` has always been an alias to `publish_foo`,
  so no functionality is lost.
- add two kwargs to `display`:
  - raw: `display({'mime': rawdata}, raw=True)`,
    so that `display` is consistent with `display_foo(raw=True)`
  - metadata: `display(obj, metadata={'key': value})`,
    adding a public API to the metadata dict.
- `display_foo` methods add an extra, optional `metadata` key.
  If `metadata` is specified, it will be placed in a sub-dict,
  keyed by the appropriate mimetype for `foo`.
- `display_foo` will no longer result in *also* displaying the plaintext
  representation of objects.
- type formatters, whether registered via `formatter.for_type`,
  or `_repr_foo_` methods may return *either* the raw data currently supported,
  or a tuple of length two, where the first element is the raw data
  and the second element is a metadata dict.
  This metadata will be added to the resulting message's metadata under the
  same mimetype key as the display data itself.
- **metadata is optional** - no metadata key must ever be required to be defined.
  It should always be allowed that the metadata dict is empty.

### Notebook format changes

The only change to the notebook format is that each output in a cell's `outputs` list
now has an extra `metadata` key, which has the same keys as the output itself.

**Note:** The shortname `json`, `png` transforms made to the output dict
were a bad decision, and will be changed to proper mime-types in the future.

### Third-party use of `metadata`

The metadata field is a black-box dict, which you can write to.
It is expected that third-party code, such as rich javascript plugins,
will want to communicate some extra information via the metadata field.
It is appropriate for developers to write their info to the metadata field,
but some basic practices should be followed to avoid conflict:

- third-parties should define their own key in the metadata,
  and only write there.

Actually, that's about it.  Just the one guideline to avoid namespace conflicts.

### Application: image shapes

Images currently cannot include shape information,
which means that one must use raw HTML to display an image with a specific shape.
Using the above proposal,
we can now support image sizes in the PNG format by defining the following metadata keys:

```json
{
  "image/png" : {
    "width": 100,
    "height": 200
  }
}
```

*The same for image/jpeg*

The `Image` display class supports these metadata via its `width` and `height` attributes.
If specified, the display size of the image will be specified.

For example:

```python
def png_with_shape(img, w, h):
    md = dict(width=w, height=h)
    return png_image_data(img), md
```

would result in the display_data message:

```json
{
    "data" : {
        "image/png" : "iVBOR...",
        ...
    },
    "metadata" : {
        "image/png" : {
            "height": 4,
            "width": 10
        }
        ...
    }
}
```

### Backward compatibility

This does add a key to the notebook format,
and adds an *interpretation* of a particular subset of the metadata,
but these are safely ignored.  In this way, notebook frontends that do not support the metadata
gracefully degrade, simply losing some layout information, but no data.

## user\_expressions / user\_variables

The `user_expressions` and `user_variables` keys in an `execute_request`
currently only support plaintext reprs, and have bad custom representations of errors.
These should simply use the display protocol, and the `status: error` behavior used everywhere else.


## multiple objects

We currently have no native mechanism for presenting containers of objects (e.g. a list of images).
The only thing we can do is display each individual image via `map(display, list_of_images)`.
Nor can we have any representation of an object that might contain multiple elements
(such as a pair of images to be displayed side-by-side),
or objects for which multiple components *should* be displayed (e.g. LaTeX + PNG).
For all of these cases, the only option is to do the entire HTML rendering kernel-side,
and use raw HTML-reprs.  This sucks quite a bit, but I don't have a good answer for it.
Perhaps the JSON reprs / jsplugins are going to be the only way to do this sort of thing.

## payload keys

Currently, display payloads list the full object path of the source, which doesn't make any sense.
It should be a simple identifier, so that moving the implementation does not need to be reflected in the frontend.