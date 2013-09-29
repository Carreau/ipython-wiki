# A multi-directory IPython notebook

<table>
<tr><td> Author </td><td> Zach Sailer &lt;zachsailer@gmail.com&gt;</td></tr>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Created </td><td> April 4, 2013</td></tr>
<tr><td> Updated </td><td> September 13, 2013</td></tr>
<tr><td> Discussion </td><td> <a href=https://github.com/ipython/ipython/issues/3166>#3166</a> </td></tr>
<tr><td> PR </td><td> <a href=https://github.com/ipython/ipython/pull/4303>#4303</a> </td></tr>

</table>

## Abstract ##
This proposes a new web service API design which supports multi-directory navigation for the IPython Notebook. It frees the user to open notebooks in a different location than the server.

## Motivation ##
Currently, it is not possible to open a notebooks in different directories under a single server. A notebook must be in the same location as the server if the user wants to open it. This prevents users from the ability to organize their notebooks into different subdirectories. It should be possible navigate to any notebook in a subdirectory under a single server.

On the development side of things, the current Notebook web service needs some standardizationand reorganization. Providing consistent messaging and error handling between the client and server will simplify the web services and make future development/growth easier.

## Summary of the changes
The following list describes a quick overview of the changes made by this proposal.

* Notebooks are no longer identified by an immutable UUID. Instead, they are tracked by their name and file-system path (relative to server's location). 
* The kernel manager and notebook manager are completely independent of one another. The kernel manager no longer maps a notebook to it's kernel using a UUID.
* A new session manager holds the mapping between kernels and notebooks.
* All handling of requests between the client and server are organized into RESTful APIs. 
* All messaging between the client and server is done using standardized JSON objects.
* The URL mapping is adjusted to reflect the new APIs and multidirectory support.

## IPython URL Mapping
The URL is adjusted for the removal of the notebook IDs. It will display the name and path (relative to server's location) of the notebook. With the reorganization of the web services, new arguments are added to the URL to call the correct API. The API URLs can be seen in the [IPython web services](#ipython_web_service) section below. This new mapping to a specific notebook is dynamic and can change with notebook rename. The current URL mapping for IPython notebook can be found [here]( https://github.com/ipython/ipython/wiki/Dev%3A-URL-mapping-of-IPython-notebook). 

The chart below shows the new URL scheme for the dashboard and notebooks (seen by users). Notice the leading arguments ```tree``` (for the dashboard) and ```notebooks``` (for notebooks).

| HTTP verb | URL | Action |
|:---:|:---:|:---|
| ```GET``` | / | redirects to "/tree" URL |
| ```GET``` | /tree | navigates user to dashboard at the top level directory of the current file system |
| ```GET``` | /tree/foo/bar | navigates user to dashboard for the "foo/bar" directory |
| ```GET``` | /tree/login | navigates to login page; if no user profile is defined, it navigates user to dashboard |
| ```GET``` | /tree/logout | logs out of current profile, and navigates user to login page |
| ```GET``` | /notebooks/foo/bar/\<notebook_name\> | go to live notebook view of the named notebook |

<span id="ipython_web_service"></span>
## IPython web services
The Notebook web service is organized into the APIs listed below. Each service handles all requests and returns the proper status code, JSON data, and URL redirects. 

This documentation describes the JSON messaging structures for the multidirectory IPython Notebook. In handling HTTP request, we pass all information between the server and client using JSON standard models. Each standard model is shown at the beginning of the sections below. This format follows to the principles described in RESTful web-services.

* Notebooks
    * [Create new notebook](#create_new_notebook)
	* [List notebooks](#list_notebooks_in_dir)
	* [Open an existing notebook](#open_named_nb)
	* [Rename notebook](#change_nb_model)
    * [Save notebook](#save_notebook)
	* [Delete notebook](#delete_nb)
* Sessions
	* [List sessions](#list_sessions)
	* [Create session](#create_session)
	* [Get session information](#get_session)
	* [Change session](#change_session)
	* [Delete a session/kill kernel](#delete_session)
* Kernels
	* [List active kernels](#list_kernels)
	* [Start a kernel](#start_kernel)
	* [Shutdown a kernel](#shutdown_kernel)

<span id='list_notebook_root'></span>
### Notebooks API
This web-service handles all HTTP requests on ".ipynb" files. The standard notebooks model in JSON is shown below. The `content` field is not always included in the notebook model because of the weight it can carry. It is typically included when the client makes a request for a particular notebook.

	{
		"name": "Untitled0.ipynb",
		"path": "/",
		"modified": "date",
        "content":{
			"metadata":{},
			"nbformat": 3,
			"nbformat_minor": 0,
			"worksheets": []
		}
	}	

<span id='create_new_notebook'></span>
#### Create new notebook ####
Creates a new notebook and names it "Untitled#.ipynb" with an incremented number.

	POST /api/foo/bar/notebooks
	
##### Response #####
Status: <font color="red">201  Created</font> 

Location: /api/notebooks/foo/bar/Untitled0.ipynb

	{
		"name": "Untitled0.ipynb",
		"path": "/foo/bar/",
		"modified": "date"
	}

<span id='list_notebooks_in_dir'></span>
#### List notebooks ####
Returns a list of standard notebook models in the "/foo/bar/" directory.

	GET /api/notebooks/foo/bar/

##### Response #####
Status: <font color="red">200  OK</font>


	[	
		{
			"name": "notebook1.ipynb",
			"path": "/foo/bar/",
			"modified": "date"
		},
		{
			"name": "notebook2.ipynb",
			"path": "/foo/bar/",
			"modified": "date"
		}
	]

<span id='open_named_nb'></span>
#### Open an existing notebook ####
Returns the standard notebook model for that named notebook and "content" value is used to display the html representation of the notebook:

	GET /api/notebooks/foo/bar/notebook1.ipynb

##### Response #####
Status: <font color="red">200  OK</font>

	{
		"name": "notebook1.ipynb",
		"path": "/foo/bar/",
		"modified": "date",
		"created": "date",
		"content":{
			"metadata":{},
			"nbformat": 3,
			"nbformat_minor": 0,
			"worksheets": []
		}
	}

<span id='change_nb_model'></span>
#### Rename notebook ####
This requests renames the notebook and returns the adjusted standard model.

	PATCH /api/notebooks/foo/bar/notebook1.ipynb

##### Input ####
*name:* new name to be used for rename.

    {
        "name": new_name.ipynb
    }

##### Response #####
Status: <font color="red">200  OK</font>

Location: /api/notebooks/foo/bar/Untitled0.ipynb

	{
		"name": "notebook1.ipynb",
		"path": "/foo/bar/",
		"modified": "new_date"
	}

<span id='save_notebook'></span>
#### Save Notebook ####
Takes the current notebook representation and saves it. Returns the standard model (without content field).

	PUT /api/notebooks/foo/bar/notebook1.ipynb

##### Input #####
*name:* notebook name.

*path:* relative path to the notebook.

*modified:* new time and date for this save.

*content:* the notebook's body.

        {
		"name": "notebook1.ipynb",
		"path": "/foo/bar/",
		"modified": "new_date"
		"content":{
			"metadata":{},
			"nbformat": 3,
			"nbformat_minor": 0,
			"worksheets": []
		}
	}

##### Response #####
Status: <font color="red">200  OK</font>

	{
		"name": "notebook1.ipynb",
		"path": "/foo/bar/",
		"modified": "new_date"
	}

<span id='delete_nb'></span>
#### Delete notebook ####
Deletes the notebook's standard model from the named location.

	DELETE /api/notebooks/foo/bar/notebook1.ipynb

##### Response #####
Status: <font color="red">204  No Content</font>


### Sessions API ###

A session maps a notebook to a kernel. When a notebook is opened, a session is created and a kernel is started. The standard model for a session is shown below:

	{
		"id": "d7753a2c-14da-4ae9-95b8-7b96b11aebe7",
		"notebook": {
			"name": "notebook1.ipynb", 
			"path": "/foo/bar/",
		}
		"kernel": {
			"id": "b7e1a137-a434-4598-846e-ee51fb06c306",
			"ws_url": ""
		}
	}

<span id='list_session'></span>
#### List sessions ####
Returns a list of standard session models for all currently running sessions.

	GET /api/sessions

##### Response #####
Status: <font color="red">200  OK</font>

	[
		{
			"id": "d7753a2c-14da-4ae9-95b8-7b96b11aebe7",
			"notebook": {
				"name": "notebook1.ipynb", 
				"path": "/foo/bar/",
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
				"path": "/foo/bar/",
			}
			"kernel": {
				"id": "4adcb1a3-2e7f-4e82-af2e-d76f5815467a",
				"ws_url": ""
			}
		}
	]

<span id='create_session'></span>
#### Create session ####
Creates a session (with a UUID) for the named notebook. Requires a JSON standard notebook model to be sent to the server before a session is created.

	POST /api/sessions

##### Input #####
*name:* name of the notebook.

*path:* relative path to the notebook.

*modified:* time and date the the notebook was last modified.

	{
		"name": "Untitled0.ipynb",
		"path": "/foo/bar/",
		"modified": "date"
	}

##### Response #####
Status: <font color="red">201  Created</font>

	{
		"id": "d7753a2c-14da-4ae9-95b8-7b96b11aebe7",
		"notebook": {
			"name": "Untitled0.ipynb", 
			"path": "/foo/bar/",
		},
		"kernel": {
			"id": "b7e1a137-a434-4598-846e-ee51fb06c306",
			"ws_url": ""
		}
	}

<span id='get_session'></span>
#### Get Session information ####

	GET /api/sessions/d7753a2c-14da-4ae9-95b8-7b96b11aebe7

##### Response #####
Status: <font color="red">200  OK</font>

{
	"id": "d7753a2c-14da-4ae9-95b8-7b96b11aebe7",
	"notebook": {
		"name": "Untitled0.ipynb", 
		"path": "/foo/bar/",
	},
	"kernel": {
		"id": "b7e1a137-a434-4598-846e-ee51fb06c306",
		"ws_url": ""
	}
}


<span id='change_session'></span>
#### Change session ####
This can be used to rename the notebook, or move a file to a new directory.

	PATCH /api/sessions/d7753a2c-14da-4ae9-95b8-7b96b11aebe7

##### Input #####
*name:* name of the notebook.

*path:* relative path to the notebook.

*modified:* time and date the the notebook was last modified.

    {
		"notebook": {
			"name": "Untitled0.ipynb",
			"path": "/foo/bar/",
		}
	}

##### Response #####
Status: <font color="red">200  OK</font>

{
	"id": "d7753a2c-14da-4ae9-95b8-7b96b11aebe7",
	"notebook": {
		"name": "new_name.ipynb", 
		"path": "/foo/bar/",
	},
	"kernel": {
		"id": "b7e1a137-a434-4598-846e-ee51fb06c306",
		"ws_url": ""
	}
}


<span id='delete_session'></span>
#### Delete a session and kill the kernel ####
Deletes the sessions and kills the kernel held in the session.

	DELETE /api/sessions/d7753a2c-14da-4ae9-95b8-7b96b11aebe7
	
##### Response #####
Status: <font color="red">204  No Content</font>

### Kernels API ###
This webservice managers all running kernels. The standard kernel model in JSON is shown below:

	{
		"id": "b7e1a137-a434-4598-846e-ee51fb06c306",
		"ws_url": ""
	}

<span id='list_kernels'></span>
#### List kernels ####
Lists the JSON data for all kernels currently running.

	GET /api/kernels

##### Response #####
Status: <font color="red">200  OK</font>

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

<span id='start_kernel'></span>
#### Start a kernel ####
Start an IPython kernel and create a UUID for the kernel. 

	POST /api/kernels

##### Response #####
Status: <font color="red">201  Created</font>

	{
		"id": "b7e1a137-a434-4598-846e-ee51fb06c306",
		"ws_url": ""
	}

<span id='shutdown_kernel'></span>
#### Shutdown the kernel ####
Kills the kernel and deletes the ID. 

	DELETE /api/kernels/0899e220-ab81-43c2-a97a-ff1af4118598

##### Response #####
Status: <font color="red">204  No Content</font>

## Things to Consider for the future

### "Contents"
The notebook dashboard only shows '.ipynb' files. In the future, we'd like to show all files in a current directory and open them in an editor-like environment. Also, we need a web service which handles all creation and deletion of folders. This IPEP doesn't include these capabilites yet, but it is something we'd like to do. 

### "Projects" 

The second thing to consider is the notion of ```projects```. A project is analogous to a repository on Github, where all files, notebooks, and sub-directores are located inside the project's file system. This IPEP only proposes implementing the ability to navigate through a hierarchical file system within a single project. When the server launches under this implementation, a dashboard opens at the top-level directory of a project (i.e. $HOME). The user can move through parent directories and sub-directories within this project, but cannot switch projects in the current server. They must relauch a server in the new project's location on their file system.

In the future, we would like to give the notebook the ability to switch between and create new projects on a single server. This will require small adjustments to this proposed URL scheme. The goal would be to create a new top-level "projects page" where the user's projects are listed. When one is clicked, the page is directed to the normal dashboard described above, with the project's file system of notebooks, sub-directories, and other files. 


## See the PR for this IPEP ##

Go to the PR [#3619](https://github.com/ipython/ipython/pull/3619).

## See discussion about this IPEP ##

Go to the IPEP 16 ~ issue #3166 [here](https://github.com/ipython/ipython/issues/3166).