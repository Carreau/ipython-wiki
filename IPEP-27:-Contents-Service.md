<table>
<tr><td> Status </td><td> Implemented </td></tr>
<tr><td> Author </td><td> Min RK &lt;benjaminrk@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> May 30, 2014</td></tr>
<tr><td> Updated </td><td> Dec 9, 2014</td></tr>
<tr><td> Implementation </td><td> <a href="https://github.com/ipython/ipython/pull/5938">PR #5938</a>
</td></tr>
</table>

## Abstract

Moving the generic file-handling code (save, load, list, delete) out of the notebook API,
and into a generic [contents](#list-contents-root) API. The only notebook-specific actions are trust and signing.

API:

- [Create new file](#create-new-files)
- [Upload a file](#upload-a-file)
- [Copy a file](#copy-a-file)
- [List contents](#list-files)
- [Get an existing file](#get-an-existing-file)
- [Rename a file](#rename-a-file)
- [Save file](#save-file)
- [Delete file](#delete-file)
- [List checkpoints](#list-checkpoints)
- [Create a checkpoint](#create-a-checkpoint)
- [Restore from a checkpoint](#restore-to-a-checkpoint)
- [Delete a checkpoint](#delete-a-checkpoint)


<span id='list-contents-root'></span>

### Contents API

This webservice handles operations on files - plain files, directories, and notebooks.

A basic contents model:

```json
{
  "name": "My Notebook.ipynb",
  "path": "foo/bar/My Notebook.ipynb",
  "type": "notebook",
  "writable": "true",
  "created": "2013-10-01T14:22:36.123456+00:00",
  "last_modified": "2013-10-02T11:29:27.616675+00:00",
  "mimetype": null,
  "content": null,
  "format": null,
}
```

All contents models have basic name, path, type, writable, created, and last_modified keys defined.

- `path` field contains the full file path. It will *not* start with `/`, and it will be `/`-delimited.
- `name` is always equivalent to the last part of the path field.
- `writable` indicates whether the requester has permission to edit the file they have requested.
- `type` will have one of three values:

   - `"directory"`
   - `"file"`
   - `"notebook"`

If the model does not contain `content`, the `content`, `format`, and `mimetype` keys will be `null`.
`mimetype` is used to specify the mime-type of `file` contents, so it is only non-null when `content` is present and `type` is `file`.

If the model includes the file content (e.g. requesting a file with GET),
then `content` will be defined, and its format described in `format`.
There are a few basic cases:

1. `type="directory"`
    - `content` will be a list containing a model for each item in the directory
    - `format="json"`
2. `type="file"`, file is UTF-8 text
    - `content` will be the text of the file as a string
    - `format="text"`
    - `mimetype="mime/type"` will be the mime-type of the file (text/plain if unknown)
3. `type="file"`, file is binary
    - `content` will be the base64-encoded content of the file as a string
    - `format="base64"`
    - `mimetype="mime/type"` will be the mime-type of the file (application/octet-stream if unknown)
4. `type="notebook"`
    - `content` will be the JSON structure of the file (dict)
    - `format="json"`


#### Only the server will provide a complete model

Client requests will never specify a whole model.
They will only specify those values that should indicate a change.

The timestamps in the file model are read-only.
A request to the server to update or upload a file model
should never contain the timestamp keys (they will be ignored).

Since `path` is specified in the URL, it is generally omitted from requests.
One exception being [renaming an existing file](#rename-file),
in which case the destination name and path are given in the model,
and the current name and path are in the URL.

<span id='create-new-file'></span>
#### Create new files

There are a few ways to create file:
creating empty untitled files, [uploading](#upload-file),
and [copying existing files](#copy-file).
In each case, POST requests are always made to a directory URL,
in which case the server picks the new file's name.
If you want to upload a file to a specific URL, make a PUT request to the full path URL.

Creates a new file. The name will be the first unused name of the form `"Untitled#.ipynb"`, with the first available integer.

    POST /api/contents/[:path]

##### Input

Creating new untitled files via POST accepts one or two optional arguments:

<dl>
  <dt>ext</dt>
  <dd>the file extension (ignored if `type` is `'notebook'`)</dd>
  <dt>type</dt>
  <dd>the model type, one of 'file', 'notebook', or 'directory'</dd>
</dl>

If both of these are unspecified, an empty file with no extension will be created.


##### Response

    status: 201 Created
    Location: /api/contents/foo/bar/Untitled0.ipynb

```json
{
  "name": "Untitled0.ipynb",
  "path": "foo/bar/Untitled0.ipynb",
  "type": "notebook",
  "created": "2013-09-01T09:15:14.12345+00:00",
  "last_modified": "2013-10-02T11:29:27.616675+00:00"
}
```

<span id='upload-file'></span>
#### Upload a file

Uploads a new file to `path`, or creates an empty directory at `path`.

    PUT /api/contents/[:path]

##### Input

<dl>
  <dt>type</dt>
  <dd>One of file, notebook, or directory</dd>
  <dt>format</dt>
  <dd>One of json (notebook), text, or base64 (file). Ignored if type is 'directory'.</dd>
  <dt>content</dt>
  <dd>The actual file content (json for notebook, text or b64 for file, ignored for directory)</dd>
</dl>

Example:

```json
{
  "path": "foo/bar/MyNotebook.ipynb",
  "name": "MyNotebook.ipynb",
  "type": "notebook",
  "format": "json",
  "content": {
    "metadata":{},
    "nbformat": 4,
    "nbformat_minor": 0,
    "cells": []
  }
}
```

##### Response

    status: 201 Created
    Location: /api/contents/foo/bar/MyNotebook.ipynb

```json
{
  "name": "MyNotebook.ipynb",
  "path": "foo/bar/MyNotebook.ipynb",
  "type": "notebook",
  "created": "2013-09-01T09:15:14.12345+00:00",
  "last_modified": "2013-09-01T09:15:14.12345+00:00"
}
```


<span id='copy-file'></span>
#### Copy a file

Create a new file from a copy of an existing file.
The new file will have a name of the form `{copy_from}-Copy#.{ext}`,
with the first available integer.

    POST /api/contents/[:path]

##### Input

<dl>
  <dt>copy_from</dt>
  <dd>The file name to copy from</dd>
</dl>

Example:

    POST /api/contents/foo/bar/

```json
{
  "copy_from" : "/baz/bat/MyNotebook.ipynb"
}
```

##### Response

    status: 201 Created
    Location: /api/contents/foo/bar/MyNotebook-Copy0.ipynb

```json
{
  "name": "MyNotebook-Copy0.ipynb",
  "path": "foo/bar/MyNotebook-Copy0.ipynb",
  "type": "notebook",
  "created": "2013-10-01T12:21:20.123456+00:00",
  "last_modified": "2013-09-01T09:15:14.12345+00:00"
}
```

<span id='list-files'></span>
#### List files

GET on a directory returns the directory model.
The `content` of a directory model is a list of models (without content)
of the directory's contents.

    GET /api/contents/[:path]

##### Response

    status: 200 OK

```json
{
  "name": "bar",
  "path": "foo/bar",
  "type": "directory",
  "created": "2013-10-01T12:21:20.123456+00:00",
  "last_modified": "2013-10-02T11:29:27.616675+00:00",
  "format": "json",
  "content": [
  {
    "name": "notebook1.ipynb",
    "path": "foo/bar/notebook1.ipynb",
    "type": "notebook",
    "created": "2013-10-01T12:21:20.123456+00:00",
    "last_modified": "2013-10-02T11:29:27.616675+00:00"
  },
  {
    "name": "file.txt",
    "path": "foo/bar/file.txt",
    "type": "file",
    "created": "2013-10-01T12:21:20.123456+00:00",
    "last_modified": "2013-10-02T11:29:27.616675+00:00"
  },
  {
    "name": "subdir",
    "path": "foo/bar/subdir",
    "type": "directory",
    "created": "2013-10-01T12:21:20.123456+00:00",
    "last_modified": "2013-10-02T11:29:27.616675+00:00"
  },
  ]
}
```



<span id='get-file'></span>
#### Get an existing file

Returns the contents model for a given file path, including the full document content.

    GET /api/contents/[:path]

##### Response

    status: 200 OK

```json
{
  "name": "notebook1.ipynb",
  "path": "foo/bar/notebook1.ipynb",
  "type": "notebook",
  "format": "json",
  "writable": true,
  "last_modified": "2013-10-02T11:29:27.616675+00:00",
  "created": "2013-10-01T12:21:20.123456+00:00",
  "content":{
    "metadata": {},
    "nbformat": 4,
    "nbformat_minor": 0,
    "cells": []
  }
}
```

A client can optionally specify a `type` and/or `format` argument via URL parameter.
When given, the Contents service shall return a model in the requested type and/or format.
If the request cannot be satisfied, e.g. `type=text` is requested, but the file is binary,
then the request shall fail with `400` and have a JSON response containing a 'reason' field,
with the value 'bad format' or 'bad type', depending on what was requested.  A client can also optionally specify a `content` argument via URL parameter.  If `content=1` is requested (the default), the full contents of the document are returned.  If `content=0` is requested, the `content` field is empty.

    GET /path/to/file.bin?format=text

##### Response

    status: 400 Bad Request

```json
{
  "error": "File is not text",
  "reason": "bad format"
}
```

<span id='rename-file'></span>
#### Rename a file

Make a PATCH request with a new `path` in the body to move a file to a new location.
This request moves the file and returns the updated model without content.

    PATCH /api/contents/[:path]

##### Input

<dl>
    <dt>path</dt>
    <dd>The new path (e.g. `foo/bar/notebook.ipynb`)</dd>
</dl>

##### Response

    status: 200 OK
    Location: /api/contents/foo/bar/notebook1.ipynb

```json
{
  "name": "notebook1.ipynb",
  "path": "foo/bar/notebook1.ipynb",
  "type": "notebook",
  "created": "2013-10-01T12:21:20.123456+00:00",
  "last_modified": "2013-10-04T14:32:19.123456+00:00"
}
```

<span id='save-file'></span>
#### Save File

Update an existing file in-place. This is how standard saves are performed.

Returns the updated model without content.

    PUT /api/contents/[:path]

##### Input
<dl>
  <dt>name</dt>
  <dd>The new filename (`my notebook.ipynb`), if changed</dd>
  <dt>path</dt>
  <dd>The new path for the file, if changed</dd>
  <dt>type</dt>
  <dd>One of 'notebook', 'file', or 'directory'</dd>
  <dt>format</dt>
  <dd>One of 'json', 'text', or 'base64'</dd>
  <dt>content</dt>
  <dd>The actual body of the document (excluding 'type=directory')<dd>
</dl>

Example:


    PUT /api/contents/foo/bar/notebook1.ipynb

```json
{
  "type": "notebook",
  "format": "json",
  "content": {
    "metadata":{},
    "nbformat": 4,
    "nbformat_minor": 0,
    "cells": []
  }
}
```

##### Response

    status: 200 OK

```json
{
  "name": "notebook1.ipynb",
  "path": "foo/bar/notebook1.ipynb",
  "type": "notebook",
  "created": "2013-10-01T12:21:20.123456+00:00",
  "last_modified": "2013-10-04T14:32:19.123456+00:00"
}
```
<span id='delete-file'></span>
#### Delete file

Deletes a file from the specified location.

    DELETE /api/contents/[:path]

##### Response

    status: 204 No Content

Will raise 400 if you attempt to delete a non-empty directory.


<span id='list-cp'></span>
#### List Checkpoints

List checkpoints for a given file. There will typically be zero or one results.

    GET /api/contents/[:path]/[:name.ipynb]/checkpoints

##### Response

    status: 200 OK

```json
[
  {
    "id" : "checkpoint-id",
    "last_modified" : "2013-10-18T23:54:40+00:00"
  }
]
```

<span id='create-cp'></span>
#### Create a Checkpoint

Create a new checkpoint with the current state of a file.
With the default FileContentsManager, only one checkpoint is supported,
so creating new checkpoints clobbers existing ones.

    POST /api/contents/[:path]/[:name.ipynb]/checkpoints

##### Response

    status: 201 Created
    Location: /api/contents/foo/bar/Untitled0.ipynb/checkpoints/[checkpoint_id]

```json
{
  "id" : "checkpoint-id",
  "last_modified" : "2013-10-18T23:54:40+00:00"
}
```

<span id='restore-cp'></span>
#### Restore to a Checkpoint

Restore a file to a particular checkpointed state.

    POST /api/contents/[:path]/[:name.ipynb]/checkpoints/[checkpoint_id]

##### Response

    status: 204 No Content


<span id='delete-cp'></span>
#### Delete a Checkpoint

Delete a checkpoint.

    DELETE /api/contents/[:path]/[:name.ipynb]/checkpoints/[checkpoint_id]

##### Response

    status: 204 No Content


<span id='changes'></span>
## Changes from IPEP 16

This section describes just the changes from the implementation in IPEP 16 / IPython 2.0.

### REST API changes

The client-visible changes to the REST API

- replace `/api/notebooks/` with `/api/contents`
- add `type` key to content model (one of `directory`, `file`, `notebook`)
- GET on a directory gives a listing of its contents
- add `format` key to content model. one of:
  - json (notebooks)
  - text (utf-8 files)
  - base64 (binary files)
- only copy with POST, never PUT
- only create empty files with POST, never PUT
- only upload with PUT, never POST
- `path` key in model is now the full path. `name` is redundant, but kept for convenience.


### Python API changes

The changes in the Python API, relevant to authors of alternative ContentsManagers (formerly NotebookManagers).

- ...services.notebooks.manager.NotebookManager moved to ...services.contents.manager.ContentsManager
- FileNotebookManager.notebook_dir is now FileContentsManager.root_dir
- Most `foo_notebook` methods renamed to just `foo`, except:
  - `get_model`
  - `file_exists`
  - `trust_notebook` left alone
- `increment_filename` takes and returns a full filename (with ext), not just the base (no ext)
- separate `path`, `name` arguments are replaced with single `path` argument containing full path