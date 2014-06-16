<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Min RK &lt;benjaminrk@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> May 30, 2014</td></tr>
<tr><td> Updated </td><td> June 16, 2014</td></tr>
<!-- <tr><td> Discussion </td><td> <a href="https://github.com/ipython/ipython/issues">Issue TBD</a> </td></tr> -->
<tr><td> Implementation </td><td> <a href="https://github.com/ipython/ipython/pull/5938">PR #5938</a>,
</td></tr>
</table>

## Abstract

Moving the generic file-handling code (save, load, list, delete) out of the notebook API,
and into a generic [contents]() API. The only notebook-specific actions are trust and signing.

* API
    * [Create new file](#create_new_file)
    * [Upload a file](#upload_file)
    * [Copy a file](#copy_file)
    * [List contents](#list_dir)
    * [Get an existing file](#get_file)
    * [Rename file](#rename_file)
    * [Save file](#save_file)
    * [Delete file](#delete_file)
    * [List checkpoints](#list_cp)
    * [Create a checkpoint](#create_cp)
    * [Restore from a checkpoint](#restore_cp)
    * [Delete a checkpoint](#delete_cp)


<span id='list_contents_root'></span>

### Contents API

This webservice handles operations on files - plain files, directories, and notebooks.

A basic contents model:

```json
{
    "name": "My Notebook.ipynb",
    "path": "foo/bar",
    "type": "notebook",
    "created": "2013-10-01T14:22:36.123456+00:00",
    "modified": "2013-10-02T11:29:27.616675+00:00",
    "content": null,
    "format": null,
}
```

All contents models have basic name, path, type, created, and modified keys defined.
`type` will have one of three values:

- `"directory"`
- `"file"`
- `"notebook"`

If the model does not contain `content`, the `content` and `format` keys will be `null`.

If the model includes the file content (e.g. requesting a file with GET),
then `content` will be defined, and its format described in `format`.
There are three basic cases:

1. `type="directory"`
  - `content` will be a list containing a model for each item in the directory
  - `format="json"`
2. `type="file"`, file is UTF-8 text
  - `content` will be the text of the file as a string
  - `format="text"`
3. `type="file"`, file is binary
  - `content` will be the base64-encoded content of the file as a string
  - `format="base64"`
4. `type="notebook"`
  - `content` will be the JSON structure of the file (dict)
  - `format="json"`


#### Only the server will provide a complete model

Client requests will never specify a whole model.
They will only specify those values that should indicate a change.

The timestamps in the file model are read-only.
A request to the server to update or upload a file model
should never contain the timestamp keys (they will be ignored).

Since the `path` and `name` are specified in the URL,
these keys are generally omitted from requests as well.
The one exception being saving an existing file to a new location,
in which case the *new* name and path are given in the model,
and the current name and path are in the URL.

The `type` field can also be omitted by the client, but will
always be provided by the servers.

<span id='create_new_file'></span>
#### Create new files

POST always creates a new file. There are a few ways to create file:
[simple creation](#create_new_file), [uploading](#upload_file),
and [copying existing files](#copy_file).
In each case, POST requests are always made to a directory URL,
in which case the server picks the new file's name.
If you want to specify the new file's name, make a PUT request to the full path URL.

Creates a new file. If a POST, the name will be the first unused name of the form `"Untitled#.ipynb"`, with the first available integer.

    POST /api/contents/[:path]
    PUT /api/contents/[:path]/[:name.ipynb]

##### Input

POST requests accept one optional argument, the file extension:
<dl>
    <dt>ext</dt>
    <dd>the file etension. If unspecified, `.ipynb` will be used</dd>
</dl>


##### Response

    status: 201 Created
    Location: /api/contents/foo/bar/Untitled0.ipynb

```json
{
    "name": "Untitled0.ipynb",
    "path": "foo/bar",
    "type": "notebook",
    "created": "2013-09-01T09:15:14.12345+00:00",
    "modified": "2013-10-02T11:29:27.616675+00:00"
}
```

<span id='upload_file'></span>
#### Upload a file

Uploads a new file to `path`. If a POST, the name will be the first unused name of the form `"Untitled#.ext"`.

    POST /api/contents/[:path]
    PUT /api/contents/[:path]/[:name.ipynb]

##### Input

<dl>
    <dt>type</dt>
    <dd>One of file, notebook, or directory</dd>
    <dt>format</dt>
    <dd>One of json (notebook), text, or base64 (file)</dd>
    <dt>content</dt>
    <dd>The actual file content (json for notebook, text or b64 for file, ignoredc for directory)</dd>
</dl>

Example:

```json
{
    "name": "MyNotebook.ipynb",
    "type": "notebook",
    "format": "json",
    "content": {
        "metadata":{},
        "nbformat": 3,
        "nbformat_minor": 0,
        "worksheets": []
    }
}
```

##### Response

    status: 201 Created
    Location: /api/contents/foo/bar/MyNotebook.ipynb

```json
{
    "name": "MyNotebook.ipynb",
    "path": "foo/bar",
    "type": "notebook",
    "created": "2013-09-01T09:15:14.12345+00:00",
    "modified": "2013-09-01T09:15:14.12345+00:00"
}
```


<span id='copy_file'></span>
#### Copy a file

Create a new file from a copy of an existing file.
If POST, the new file will have a name of the form `{copy_from}-Copy#.{ext}`,
with the first available integer.

    POST /api/contents/[:path]
    PUT /api/contents/[:path]/[:name.ipynb]

##### Input

<dl>
    <dt>copy_from</dt>
    <dd>The file name to copy from</dd>
</dl>

Example:

    POST /appi/contents/foo/bar

```json
{
    "copy_from" : "MyNotebook.ipynb"
}
```

##### Response

    status: 201 Created
    Location: /api/contents/foo/bar/MyNotebook-Copy0.ipynb

```json
{
    "name": "MyNotebook-Copy0.ipynb",
    "path": "foo/bar",
    "type": "notebook",
    "created": "2013-10-01T12:21:20.123456+00:00",
    "modified": "2013-09-01T09:15:14.12345+00:00"
}
```

<span id='list_files'></span>
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
  "path": "foo",
  "type": "directory",
  "created": "2013-10-01T12:21:20.123456+00:00",
  "modified": "2013-10-02T11:29:27.616675+00:00",
  "format": "json",
  "content": [
    {
        "name": "notebook1.ipynb",
        "path": "foo/bar",
        "type": "notebook",
        "created": "2013-10-01T12:21:20.123456+00:00",
        "modified": "2013-10-02T11:29:27.616675+00:00"
    },
    {
        "name": "file.txt",
        "path": "foo/bar",
        "type": "file",
        "created": "2013-10-01T12:21:20.123456+00:00",
        "modified": "2013-10-02T11:29:27.616675+00:00"
    },
    {
        "name": "subdir",
        "path": "foo/bar",
        "type": "directory",
        "created": "2013-10-01T12:21:20.123456+00:00",
        "modified": "2013-10-02T11:29:27.616675+00:00"
    },
  ]
}
```



<span id='get_file'></span>
#### Get an existing file

Returns the contents model for a given file path, including the full document content.

    GET /api/contents/[:path]/[:name.ext]

##### Response

    status: 200 OK

```json
{
    "name": "notebook1.ipynb",
    "path": "foo/bar",
    "type": "notebook",
    "format": "json",
    "modified": "2013-10-02T11:29:27.616675+00:00",
    "created": "2013-10-01T12:21:20.123456+00:00",
    "content":{
        "metadata": {},
        "nbformat": 3,
        "nbformat_minor": 0,
        "worksheets": []
    }
}
```

<span id='rename_file'></span>
#### Rename file

This request renames the file and returns the updated model without content.

    PATCH /api/contents/[:path]/[:name.ipynb]

##### Input

<dl>
    <dt>name</dt>
    <dd>The new filename (e.g. `notebook.ipynb`)</dd>
    <dt>path</dt>
    <dd>The new path (e.g. `foo/bar`)</dd>
</dl>

##### Response

    status: 200 OK
    Location: /api/contents/foo/bar/notebook1.ipynb

```json
{
    "name": "notebook1.ipynb",
    "path": "foo/bar",
    "type": "notebook",
    "created": "2013-10-01T12:21:20.123456+00:00",
    "modified": "2013-10-04T14:32:19.123456+00:00"
}
```

<span id='save_file'></span>
#### Save File

Update an existing file in-place. This is how standard saves are performed.
If the path and/or name of the file are specified in the model,
the file will be saved to the new location specified in the model.
After doing this, future PUT requests must use the URL for the new path.

Returns the updated model without content.

    PUT /api/contents/[:path]/[:name.ipynb]

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

```json
{
    "name": "notebook1.ipynb",
    "path": "foo/bar",
    "content": {
        "metadata":{},
        "nbformat": 3,
        "nbformat_minor": 0,
        "worksheets": []
    }
}
```

##### Response

    status: 200 OK

```json
{
    "name": "notebook1.ipynb",
    "path": "foo/bar",
    "type": "notebook",
    "created": "2013-10-01T12:21:20.123456+00:00",
    "modified": "2013-10-04T14:32:19.123456+00:00"
}
```
<span id='delete_file'></span>
#### Delete file

Deletes a file from the specified location.

    DELETE /api/contents/[:path]/[:name.ipynb]

##### Response

    status: 204 No Content

Will raise 400 if you attempt to delete a non-empty directory


<span id='list_cp'></span>
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

<span id='create_cp'></span>
#### Create a Checkpoint

Create a new checkpoint with the current state of a file.
With the default FileContentsManager, only one checkpoint is supported,
so creating new checkpoints clobbers existing ones.

    POST /api/contents/[:path]/[:name.ipynb]/checkpoints

##### Response

    status: 201 Created

```json
{
    "id" : "checkpoint-id",
    "last_modified" : "2013-10-18T23:54:40+00:00"
}
```

<span id='restore_cp'></span>
#### Restore to a Checkpoint

Restore a file to a particular checkpointed state.

    POST /api/contents/[:path]/[:name.ipynb]/checkpoints/[checkpoint_id]

##### Response

    status: 204 No Content


<span id='delete_cp'></span>
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
- add `type` key to content model (one of `dir`, `file`, `notebook`)
- GET on a directory gives a listing of its contents
- add `format` key to content model. one of:
  - json (notebooks)
  - text (utf-8 files)
  - base64 (binary files)
- POST

### Python API changes

The changes in the Python API, relevant to authors of alternative ContentsManagers (formerly NotebookManagers).

- ...services.notebooks.manager.NotebookManager moved to ...services.contents.manager.ContentsManager
- FileNotebookManager.notebook_dir is now FileContentsManager.root_dir
- Most `foo_notebook` methods renamed to just `foo`, except:
  - `get_model`
  - `file_exists`
  - `trust_notebook` left alone
- `increment_filename` takes and returns a full filename (with ext), not just the base (no ext)
