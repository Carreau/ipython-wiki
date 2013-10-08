# A multi-directory IPython notebook

<table>
<tr><td> Author </td><td> Zach Sailer &lt;zachsailer@gmail.com&gt;</td></tr>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Created </td><td> April 4, 2013</td></tr>
<tr><td> Updated </td><td> October 2, 2013</td></tr>
<tr><td> Discussion </td><td> <a href=https://github.com/ipython/ipython/issues/3166>#3166</a> </td></tr>
<tr><td> PR </td><td> <a href=https://github.com/ipython/ipython/pull/4303>#4303</a> </td></tr>

</table>

## Abstract

This proposes a new web service API design which supports multi-directory navigation for the
IPython Notebook. It frees the user to open notebooks in a different location than the server.

## Motivation

Currently, it is not possible to open a notebooks in different directories under a single server. A
notebook must be in the same location as the server if the user wants to open it. This prevents
users from the ability to organize their notebooks into different subdirectories. It should be
possible navigate to any notebook in a subdirectory under a single server.

On the development side of things, the current Notebook web service needs some standardization and
reorganization. Providing consistent messaging and error handling between the client and server
will simplify the web services and make future development/growth easier.

## Summary of the changes

The following list describes a quick overview of the changes made by this proposal.

* Notebooks are no longer identified by an immutable UUID. Instead, they are tracked by their name and file-system path (relative to server's location). 
* The kernel manager and notebook manager are completely independent of one another. The kernel manager no longer maps a notebook to it's kernel using a UUID.
* A new session manager holds the mapping between kernels and notebooks.
* All handling of requests between the client and server are organized into RESTful APIs. 
* All messaging between the client and server is done using standardized JSON objects.
* The URL mapping is adjusted to reflect the new APIs and multidirectory support.

## IPython URL Mapping

The URL is adjusted for the removal of the notebook IDs. It will display the name and path
(relative to server's location) of the notebook. With the reorganization of the web services, new
arguments are added to the URL to call the correct API. The API URLs can be seen in the [IPython
web services](#ipython_web_service) section below. This new mapping to a specific notebook is
dynamic and can change with notebook rename. The current URL mapping for IPython notebook can be
found [here]( https://github.com/ipython/ipython/wiki/Dev%3A-URL-mapping-of-IPython-notebook).

The chart below shows the new URL scheme for the dashboard and notebooks (seen by users).
Note the first element `tree` (for the dashboard) and `notebooks` (for notebooks).

| HTTP verb | URL | Action |
|:---:|:---:|:---|
| ```GET``` | / | redirects to "/tree" URL |
| ```GET``` | /tree | navigates user to dashboard at the top level directory of the current file system |
| ```GET``` | /tree/foo/bar | navigates user to dashboard for the "foo/bar" directory |
| ```GET``` | /login | navigates to login page; if no user profile is defined, it navigates user to dashboard |
| ```GET``` | /logout | logs out of current profile, and navigates user to login page |
| ```GET``` | /notebooks/foo/bar/[:notebook_name] | go to live notebook view of the named notebook |

<span id="ipython_web_service"></span>
## IPython web services

The Notebook web service is organized into the APIs listed below. Each service handles all requests and returns the proper status code, JSON data, and URL redirects. 

This documentation describes the JSON messaging structures for the multidirectory IPython Notebook. In handling HTTP request, we pass all information between the server and client using JSON standard models. Each standard model is shown at the beginning of the sections below. This format follows to the principles described in RESTful web-services.

* Notebooks
    * [Create new notebook](#create_new_notebook)
    * [Upload a notebook](#upload_notebook)
    * [Copy a notebook](#copy_notebook)
    * [List notebooks](#list_notebooks_in_dir)
    * [Open an existing notebook](#open_named_nb)
    * [Rename notebook](#change_nb_model)
    * [Save notebook](#save_notebook)
    * [Delete notebook](#delete_nb)
* Kernels
    * [List active kernels](#list_kernels)
    * [Start a kernel](#start_kernel)
    * [Shutdown a kernel](#shutdown_kernel)
* Sessions
    * [List sessions](#list_sessions)
    * [Create session](#create_session)
    * [Get session information](#get_session)
    * [Change session](#change_session)
    * [Delete a session/kill kernel](#delete_session)

<span id='list_notebook_root'></span>
### Notebooks API

This web-service handles all HTTP requests on ".ipynb" files. The notebooks model in JSON is shown below. The `content` field is not always included in the notebook model because of the weight it can carry. It is typically included when the client makes a request for a particular notebook.

```json
{
    "name": "My Notebook.ipynb",
    "path": "foo/bar",
    "modified": "2013-10-01T14:22:36.123456+00:00",
    "modified": "2013-10-02T11:29:27.616675+00:00",
    "content":{
        "metadata":{},
        "nbformat": 3,
        "nbformat_minor": 0,
        "worksheets": []
    }
}
```

**Note about paths:** When a `path` key appears in the RESTful API,
it is always the path relative to the root notebook server directory.
In replies from the notebook server, this path SHALL NOT start with '/',
and SHALL NOT end with '/'.
In requests to the server, it SHOULD NOT start or end with '/'.
The path separator MUST BE '/', it is not platform dependent.
The path SHALL BE unicode, not url-escaped.

<span id='create_new_notebook'></span>
#### Create new notebook

POST always creates a new notebook. There are a few ways to create notebooks:
[simple creation](#create_new_notebook), [uploading](#upload_notebook),
and [copying existing notebooks](#copy_notebook).

Creates a new notebook and names it `"Untitled#.ipynb"` with the lowest number not already taken.

    POST /api/notebooks/[:path]

##### Response

    status: 201 Created
    Location: /api/notebooks/foo/bar/Untitled0.ipynb

```json
{
    "name": "Untitled0.ipynb",
    "path": "foo/bar",
    "created": "2013-09-01T09:15:14.12345+00:00",
    "modified": "2013-10-02T11:29:27.616675+00:00"
}
```

<span id='upload_notebook'></span>
#### Upload a notebook

Uploads a new notebook with `name` in directory `path`.
If you don't specify `content`, a new empty notebook will be created with the specified name.

    POST /api/notebooks/[:path]/[:name.ipynb]

##### Input

<dl>
    <dt>content</dt>
    <dd>The actual notebook structure</dd>
</dl>

Example:

```json
{
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
    Location: /api/notebooks/foo/bar/MyNotebook.ipynb

```json
{
    "name": "MyNotebook.ipynb",
    "path": "foo/bar",
    "created": "2013-09-01T09:15:14.12345+00:00",
    "modified": "2013-09-01T09:15:14.12345+00:00"
}
```


<span id='copy_notebook'></span>
#### Copy a notebook

Create a new notebook from a copy of the notebook with `name` in `path`.
The new notebook will have a name of the form `name-Copy#.ipynb`,
with the first available integer.

    POST /api/notebooks/[:path]/[:name.ipynb]/copy


##### Response

    status: 201 Created
    Location: /api/notebooks/foo/bar/MyNotebook-Copy0.ipynb

```json
{
    "name": "MyNotebook-Copy0.ipynb",
    "path": "foo/bar",
    "created": "2013-10-01T12:21:20.123456+00:00",
    "modified": "2013-09-01T09:15:14.12345+00:00"
}
```

<span id='list_notebooks_in_dir'></span>
#### List notebooks

Returns a list of notebook models in the directory `path`.

    GET /api/notebooks/[:path]

##### Response

    status: 200 OK

```json
[
    {
        "name": "notebook1.ipynb",
        "path": "foo/bar",
        "created": "2013-10-01T12:21:20.123456+00:00",
        "modified": "2013-10-02T11:29:27.616675+00:00"
    },
    {
        "name": "notebook2.ipynb",
        "path": "foo/bar",
        "created": "2013-10-01T12:21:20.123456+00:00",
        "modified": "2013-10-02T11:29:27.616675+00:00"
    }
]
```

<span id='open_named_nb'></span>
#### Open an existing notebook

Returns the notebook model for a given notebook path, including the full document content.

    GET /api/notebooks/[:path]/[:name.ipynb]

##### Response

    status: 200 OK

```json
{
    "name": "notebook1.ipynb",
    "path": "foo/bar",
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

<span id='change_nb_model'></span>
#### Rename notebook

This request renames the notebook and returns the updated model without content.

    PATCH /api/notebooks/[:path]/[:name.ipynb]

##### Input

<dl>
    <dt>name</dt>
    <dd>The new notebook filename (e.g. `notebook.ipynb`)</dd>
    <dt>path</dt>
    <dd>The new notebook path (e.g. `foo/bar`)</dd>
</dl>

##### Response

    status: 200 OK
    Location: /api/notebooks/foo/bar/notebook1.ipynb

```json
{
    "name": "notebook1.ipynb",
    "path": "foo/bar",
    "created": "2013-10-01T12:21:20.123456+00:00",
    "modified": "2013-10-04T14:32:19.123456+00:00"
}
```

<span id='save_notebook'></span>
#### Save Notebook

Update an existing notebook in-place. This is how standard saves are performed.
If the path and/or name of the notebook are specified in the model,
the notebook will be saved to the new location specified in the model.
After doing this, future PUT requests must use the URL for the new path.

Returns the updated notebook model without content.

    PUT /api/notebooks/[:path]/[:name.ipynb]

##### Input
<dl>
    <dt>name</dt>
    <dd>The new notebook name (`my notebook.ipynb`), if changed</dd>
    <dt>path</dt>
    <dd>The new path for the notebook, if changed</dd>
    <dt>content</dt>
    <dd>The actual body of the notebook document<dd>
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
    "created": "2013-10-01T12:21:20.123456+00:00",
    "modified": "2013-10-04T14:32:19.123456+00:00"
}
```
<span id='delete_nb'></span>
#### Delete notebook

Deletes a notebook from the specified location.

    DELETE /api/notebooks/[:path]/[:name]

##### Response

    status: 204 No Content


### Kernels API

This webservice manages all running kernels. The kernel model in JSON is shown below:

```json
{
    "id": "b7e1a137-a434-4598-846e-ee51fb06c306",
    "ws_url": ""
}
```

<span id='list_kernels'></span>
#### List kernels

Lists the JSON data for all kernels currently running.

    GET /api/kernels

##### Response

    status: 200 OK

```json
[
    {
        "id": "b7e1a137-a434-4598-846e-ee51fb06c306",
        "ws_url": ""
    },
    {
        "id": "4f567fb0-2455-4bc9-a137-69daac27e9a2",
        "ws_url": ""
    }
]
```

<span id='start_kernel'></span>
#### Start a kernel
Start an IPython kernel and create a UUID for the kernel. 

    POST /api/kernels

##### Response

    status: 201 Created

```json
{
    "id": "b7e1a137-a434-4598-846e-ee51fb06c306",
    "ws_url": ""
}
```

<span id='shutdown_kernel'></span>
#### Shutdown the kernel
Kills the kernel and deletes the ID. 

    DELETE /api/kernels/[:kernel-id]

##### Response

    status: 204 No Content


### Sessions API

A session maps a notebook to a kernel. When a notebook is opened in the Web Notebook,
a session is created and a kernel is started. The model for a session:

```json
{
    "id": "d7753a2c-14da-4ae9-95b8-7b96b11aebe7",
    "notebook": {
        "name": "notebook1.ipynb", 
        "path": "foo/bar",
    }
    "kernel": {
        "id": "b7e1a137-a434-4598-846e-ee51fb06c306",
        "ws_url": ""
    }
}
```

<span id='list_sessions'></span>
#### List sessions
Returns a list of all currently running sessions.

    GET /api/sessions

##### Response

    status: 200 OK
    
```json
[
    {
        "id": "d7753a2c-14da-4ae9-95b8-7b96b11aebe7",
        "notebook": {
            "name": "notebook1.ipynb", 
            "path": "foo/bar",
        }
        "kernel": {
            "id": "b7e1a137-a434-4598-846e-ee51fb06c306",
            "ws_url": ""
        }
    },
    {
        "id": "b4dfa62c-0e91-4111-9d1f-0c59069c6602",
        "notebook": {
            "name": "notebook1.ipynb", 
            "path": "foo/bar",
        }
        "kernel": {
            "id": "4adcb1a3-2e7f-4e82-af2e-d76f5815467a",
            "ws_url": ""
        }
    }
]
```

<span id='create_session'></span>
#### Create a Session

Creates a new Session for a given notebook.

    POST /api/sessions

##### Input
<dl>
    <dt>name</dt>
    <dd>The filename of the notebook</dd>
    <dt>path</dt>
    <dd>Path to the notebook's directory</dd>
</dl>


```json
{
    "name": "Untitled0.ipynb",
    "path": "foo/bar"
}
```

##### Response

    status: 201 Created

```json
{
    "id": "d7753a2c-14da-4ae9-95b8-7b96b11aebe7",
    "notebook": {
        "name": "Untitled0.ipynb", 
        "path": "foo/bar",
    },
    "kernel": {
        "id": "b7e1a137-a434-4598-846e-ee51fb06c306",
        "ws_url": ""
    }
}
```

<span id='get_session'></span>
#### Get Session information

    GET /api/sessions/[:session_id]

##### Response

    status: 200 OK

```json
{
    "id": "d7753a2c-14da-4ae9-95b8-7b96b11aebe7",
    "notebook": {
        "name": "Untitled0.ipynb",
        "path": "foo/bar",
    },
    "kernel": {
        "id": "b7e1a137-a434-4598-846e-ee51fb06c306",
        "ws_url": ""
    }
}
```

<span id='change_session'></span>
#### Change session
This can be used to rename the notebook, or move it to a new directory.

    PATCH /api/sessions/[:session_id]

##### Input

<dl>
    <dt>name</dt>
    <dd>The new filename of the notebook</dd>
    <dt>path</dt>
    <dd>The new notebook's path</dd>
</dl>

##### Response

    status: 200 OK

```json
{
    "id": "d7753a2c-14da-4ae9-95b8-7b96b11aebe7",
    "notebook": {
        "name": "new_name.ipynb", 
        "path": "foo/bar",
    },
    "kernel": {
        "id": "b7e1a137-a434-4598-846e-ee51fb06c306",
        "ws_url": ""
    }
}
```


<span id='delete_session'></span>
#### Delete a session

Deleting a session terminates the session's kernel, and removes the session mapping.

    DELETE /api/sessions/[:session_id]
    
##### Response

    status: 204 No Content

## Things to Consider for the future

### "Contents"

The notebook dashboard only shows '.ipynb' files. In the future, we'd like to show all files in a
current directory and open them in an editor-like environment. Also, we need a web service which
handles all creation and deletion of folders. This IPEP doesn't include these capabilities yet, but
it is something we'd like to do.

### "Projects" 

The second thing to consider is the notion of ```projects```. A project is analogous to a
repository on Github, where all files, notebooks, and sub-directories are located inside the
project's file system. This IPEP only proposes implementing the ability to navigate through a
hierarchical file system within a single project. When the server launches under this
implementation, a dashboard opens at the top-level directory of a project (i.e. $HOME). The user
can move through parent directories and sub-directories within this project, but cannot switch
projects in the current server. They must relaunch a server in the new project's location on their
file system.

In the future, we would like to give the notebook the ability to switch between and create new
projects on a single server. This will require small adjustments to this proposed URL scheme. The
goal would be to create a new top-level "projects page" where the user's projects are listed. When
one is clicked, the page is directed to the normal dashboard described above, with the project's
file system of notebooks, sub-directories, and other files.


## See the PR for this IPEP

Go to the PR [#4303](https://github.com/ipython/ipython/pull/4303).

## See discussion about this IPEP

Go to the IPEP 16 discussion issue [#3166](https://github.com/ipython/ipython/issues/3166).