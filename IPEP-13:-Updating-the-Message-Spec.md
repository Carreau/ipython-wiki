<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Min RK &lt;benjaminrk@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> April 6, 2013</td></tr>
<tr><td> Updated </td><td> April 8, 2014</td></tr>
<tr><td> Implementation </td><td> <a href="https://github.com/ipython/ipython/pull/3190">PR 3190</a>, <a href="https://github.com/ipython/ipython/pull/4536">4536</a> </td></table>

See also: [[IPEP 24: completion and object_info]] for updates to completion and object_info

We've learned a lot in our first 18 months with the message spec,
and we got a remarkable amount right.
But we didn't get everything right, and we want to clean some things up prior to 1.0.

## Renamed message types

A few names need to change, because they are Python-specific, and our message spec is not.

**NOTE: Since these names have notebook format implications, they will not be changed until 3.0**

- `pyin` will become `execute_input`
- `pyout` will become `execute_result`
- `pyerr` will become `error`

## `pyout` vs `display_data`

`pyout` and `display_data` are identical messages, with `pyout` having an additional `prompt_number` field.
It was proposed to merge these two, but preserving the distinction between `result` and `side effect` at the message level is desirable.

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

## multiple objects

**Nothing will be done on this issue for this IPEP**

<del>
We currently have no native mechanism for presenting containers of objects (e.g. a list of images).
The only thing we can do is display each individual image via `map(display, list_of_images)`.
Nor can we have any representation of an object that might contain multiple elements
(such as a pair of images to be displayed side-by-side),
or objects for which multiple components *should* be displayed (e.g. LaTeX + PNG).
For all of these cases, the only option is to do the entire HTML rendering kernel-side,
and use raw HTML-reprs.  This sucks quite a bit, but I don't have a good answer for it.
</del>

Perhaps JSON reprs / js widgets are going to be the only way to do this sort of thing.

## getpass

Add a `password` boolean key to `input_request`, to indicate that frontends should not echo input.


## Message headers

Message headers will have the protocol version (5.0 as of IPython 3.0) as a string.
If the message header lacks a version key, it should be assumed to be 4.1,
the last version before this update.

## kernel_info

- version keys in `kernel_info_reply` messages will be strings, not `version_info`-style lists.
- A `banner` key is added, for the kernel's contribution to console frontend banners.
- instead of `ipython_version`, `implementation` and `implementation_version` keys are added.

The IPython kernel's kernel_info_reply in IPython 3.0:

```python
{
  "protocol_version" : "5.0.0",
  "implementation" : "ipython",
  "implementation_version" : "3.0.0",
  "language" : "python",
  "language_version" : "3.4.0",
  "banner" : "Python 3.4.0 (/usr/local/bin/python)\nIPython 3.0.0\n",
}
```

## mime-bundles

The mime-type structure used in `display_data` is useful in more contexts than just display messages.
It will be adopted in other places where representation information is requested, specifically:

- object_info_reply
- user_expressions
- pager payload

A mime-bundle takes the form:

```python
{
  "status" : "ok",
  "data" : {
    "mime-type" : "mime-type-data",
  },
  "metadata" : {
    "mime-type" : {
      "metadata" : "about mime-type",
    }
  }
}
```

- if `status == 'ok'`, then 'data' and 'metadata' must be defined.
- if `status == 'error'`, then 'ename', 'evalue', and 'etraceback' must be defined,
  as described in the message spec.

In some contexts, the `status = 'error'` case may not make sense,
but `status=='ok'` should always be checked before working with `data`.

### object_info_reply

[[IPEP 24: completion and object_info]] describes turning object_info_reply with a mime-bundle message.

### JSON data

The `application/json`` key in a mime bundle stores JSON-serialized data, rather than JSON-able data.
This results in serializing this data twice, and having to deserialize it twice on the frontend.
The JSON key will now be unserialized, rather than the serialized form.

## user\_expressions / user\_variables

Since variable names are expressions, user_variables will be removed,
and only user_expressions remaining.

The `user_expressions` and `user_variables` keys in an `execute_request`
currently only support plaintext reprs, and have bad custom representations of errors.
These will be replaced with mime-bundles (like display).

## Payloads

**The entire Payload model will be revisited in a separate IPEP.**

Currently, display payloads list the full object path of the source, which doesn't make any sense.
It should be a simple identifier, so that moving the implementation does not need to be reflected in the frontend.

### pager mime-bundle

The pager payload will also become a mime-bundle, instead of having just a 'text' key.

An example `execute_reply`:

```python
{
  "status" : "ok",
  "execution_count" : 4,
  "user_expressions" : {
    "foo" : {
      "status": "ok",
      "data" : {
        "text/plain" : "5",
      },
      "metadata" : {},
    }
  },
  "payloads" : [
    {
      "source" : "pager",
      "status" : "ok",
      "data" : {
        "text/plain" : "pager data...",
      },
      "metadata" : {},
    },
  ]
}
```

