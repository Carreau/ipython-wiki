# A Multi-directory IPython notebook dashboard

<table>
<tr><td> Author </td><td> Zach Sailer &lt;zachsailer@gmail.com&gt;</td></tr>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Created </td><td> April 4, 2013</td></tr>
<tr><td> Updated </td><td> July 12, 2013</td></tr>
<tr><td> Discussion </td><td> <a href=https://github.com/ipython/ipython/issues/3166>#3166</a> </td></tr>
<tr><td> PR </td><td> <a href=https://github.com/ipython/ipython/pull/3619>#3619</a> </td></tr>

</table>

## Abstract ##
The current notebook dashboard only allows the user to open notebooks in their current directory location. It does not allow you to navigate through or open notebooks in subdirectories. This proposal describes a new URL mapping scheme to adjust for a multi-directory dashboard system and explores the possibility of implementing such a system. This calls for a web service redesign and new API structure.

## Motivation ##
If you have notebooks saved or would like to start new notebooks in subdirectories, you have to either restart the current IPython notebook server in that subdirectory, or start a new notebook server there. It would be convenient if you could click a button on your current dashboard and have it do the work for you. Then, you could pass back and forth through directories without starting up a new servers everytime. This would help with organizing a project and relieve frustration. On the development side of things, this will clean up and standardize the way we pass information between the client and server.

## Proposed URL Mapping ##

Before this new dashboard can be implemented, the URL mapping of IPython notebook would need to be adjusted to allow for the proper storing of notebooks and path changing. One can see the current IPython notebook URL mapping [here]( https://github.com/ipython/ipython/wiki/Dev%3A-URL-mapping-of-IPython-notebook). 

This proposal outlines the URL mapping scheme for this new multi-directory dashboard. It will follow Github's style, where a standard JSON model is created for each HTTP request and sent between the server and client. Each request will follow a RESTful design and offer an easy, practical URL path navigation. This does not change anything drastically for the user experience; it simply takes it a step further to provide access to subdirectories in the dashboard and uses less bandwidth with each HTTP request. Therefore, you will see similar design in the IPython Development wiki pages. 

The chart below shows the new proposed URL scheme for web-services in the notebook, where "foo/bar" is a path to a subdirectory: 

| HTTP verb | New URL | Action |
|:---:|:---:|:---|
| ```GET``` | /api/notebooks/foo/bar | returns a list of dicts (standard models) of notebook models found in the "foo/bar" directory path | 
| ```POST``` | /api/notebooks/foo/bar | if sending a body, saves that body as a new notebook in "foo/bar"; if no body, create a new notebook in "foo/bar" | 
| ```GET``` | /api/notebooks/foo/bar/\<notebook_name\> | returns the json data for the notebook in "foo/bar" |
| ```PUT``` | /api/notebooks/foo/bar/\<notebook_name\> | saves an existing notebook with body data in "foo/bar" |
| ```PATCH``` | /api/notebooks/foo/bar/\<notebook_name\> | changes specified keys and values of the notebook model |
| ```DELETE``` | /api/notebooks/foo/bar/\<notebook_name\> | deletes the notebook in "foo/bar" | 

*See the notebook model stucture in the web service documentation [here.](https://gist.github.com/Zsailer/5767194)*

The same URL pattern will be made to the other web-services (i.e. ```/kernels``` and ```/clusters```) shown in the current URL mapping. 

Along side the IPython web-services, there are other website URL's that will need to change as well. The chart below shows the proposed changes for the new website URL scheme of the notebook.

*Notice the addition of "/tree" to the website URL's.* This is to prevent any confusion if the user has a subdirectory named "/notebooks". 

| HTTP verb | URL | Action |
|:---:|:---:|:---|
| ```GET``` | /tree | navigates user to dashboard at the top level directory of the current file system |
| ```GET``` | / | redirects to "/tree" URL |
| ```GET``` | /tree/foo/bar | navigates user to dashboard for the "foo/bar" directory |
| ```GET``` | /tree/login | navigates to login page; if no user profile is defined, it navigates user to dashboard |
| ```GET``` | /tree/logout | logs out of current profile, and navigates user to login page |
| ```GET``` | /notebooks/foo/bar/\<notebook_name\> | go to live notebook view of the named notebook |
| ```GET``` | /notebooks/foo/bar/new | creates a new notebook with profile (or default, if no profile exists) settings in"foo/bar" |
| ```GET``` | /notebooks/foo/bar/\<notebook_name\>/copy | copies notebook of given name to "foo/bar" directory |
| ```GET``` | /notebooks/foo/bar/\<notebook_name\>/print | prints the notebook with the given name; if notebook name doesn't exist, displays error message |
| ```GET``` | /edit/foo/bar | go to live edit view for text files |

*See the standard model stuctures for these services in the web service documentation [here.](https://gist.github.com/Zsailer/5767194)*

This IPEP also proposes the addition of two new web services, ```sessions``` and ```contents```. A session is a mapping manager that links a kernel to a notebook. In the current notebook, the notebook_id and kernel information is entangled and will not allow a notebook to open without a kernel. This is completely removed using a session.

| HTTP verb | URL | Action |
|:---:|:---:|:---|
| ```GET``` | /api/session | returns a list of dicts (standard models) of all currently running sessions |
| ```POST``` | /api/session | creates a new session and returns the session model for that session |
| ```GET``` | /api/session/\<session_id\> | returns the session model for the session with given ID |
| ```PATCH``` | /api/session/\<session_id\> | changes session model for session with given ID |
| ```DELETE``` | /api/session/\<session_id\> | deletes session with given ID. |

The content's web service will contain all the information for subdirectories and files that are not notebooks.

| HTTP verb | URL | Action |
|:---:|:---:|:---|
| ```GET``` | /api/contents/foo/bar | returns a list of dicts (standard models) of all files in current directory |
| ```GET``` | /api/contents/foo/bar/\<file_name\> | returns the standard model for the file with the given name |
| ```DELETE``` | /api/contents/foo/bar/\<file_name\>  | deletes named filed |

*See the standard model stuctures for these services in the web service documentation [here.](https://gist.github.com/Zsailer/5767194)*

## Things to Consider for the future

### Notebook Sharing 

One question is whether we want the URL to continue using notebook ID, rather than the notebook's name. Keeping the ID leaves the URL as a hybrid between a path name (i.e. "foo/bar") and notebook ID. It might be nice to have the URL be consistent and say, "/foo/bar/notebook1" rather than a "foo/bar/long-stream-of-numbers-at-the-end". 

The difficulty with changing the URL to filenames rather than IDs, however, is found in the sharing of notebooks: 

1. If a notebook is being shared, and it is renamed or changed by user#1, those changes are not made on user#2's notebook page. If they save their unchanged notebook, all progress is lost for the user#1. User#2 would need to reload the page to avoid losing the progress of user#1. 
2. The URL needs to change for both users if a notebook is renamed. The notebook ID style URL stays consistent despite any changes made.

### "Projects" 

The second thing to consider is the notion of ```projects```. A project is analogous to a repository on Github, where all files, notebooks, and sub-directores are located inside the project's file system. This IPEP only proposes implementing the ability to navigate through a hierarchical file system within a single project. When the server launches under this implementation, a dashboard opens at the top-level directory of a project (i.e. $HOME). The user can move through parent directories and sub-directories within this project, but cannot switch projects in the current server. They must relauch a server in the new project's location on their file system.

In the future (perhaps as a second stage to this proposal), we would like to give the notebook the ability to switch between and create new projects on a single server. This will require small adjustments to this proposed URL scheme. The goal would be to create a new top-level "projects page" where the user's projects are listed. When one is clicked, the page is directed to the normal dashboard described above, with the project's file system of notebooks, sub-directories, and other files. 

The new URL mapping would require the addition of a ```/<project_name>``` to the URL hierarchy. The tables below show a possible mapping for this project's notion. 

| HTTP verb | New URL | Action |
|:---:|:---:|:---|
| ```GET``` | /api/\<project_name\>/notebooks/foo/bar | returns a dict with the project's "foo/bar" sub-directories, files, notebooks and path | 
| ```POST``` | /api/\<project_name\>/notebooks/foo/bar | if sending a body, saving that body as a new notebook in the project's "foo/bar" location; if no body, create a new notebook in "foo/bar" | 
| ```GET``` | /api/\<project_name\>/notebooks/foo/bar/\<notebook_id\> | returns the json data for the notebook with the given ID in "/<project_name>foo/bar" |
| ```PUT``` | /api/\<project_name\>/notebooks/foo/bar/\<notebook_id\> | saves an existing notebook with body data in "/<project_name>foo/bar" |
| ```DELETE``` | /api/\<project_name\>/notebooks/foo/bar/\<notebook_id\> | deletes the notebook with the given ID in "/<project_name>foo/bar" | 

The next chart shows the changes that would be needed for the website URL mapping. 

| HTTP verb | URL | Action |
|:---:|:---:|:---|
| ```GET``` | / | navigate's user the the a "project's page" which lists all the projects on the local file-system |
| ```GET``` | /\<project_name\>/tree | navigates user to the top-level dashboard with the specified project name |
| ```GET``` | /\<project_name\>/tree/foo/bar | navigates user to dashboard for the "/\<project_name\>/foo/bar" directory |
| ```GET``` | /\<project_name\>/tree/login | navigates to login page; if no user profile is defined, it navigates user to the "project's page" |
| ```GET``` | /\<project_name\>/tree/logout | logs out of current profile, and navigates user to login page |
| ```GET``` | /\<project_name\>/notebooks/foo/bar/\<notebook_id\> | open the notebook with the given ID |
| ```GET``` | /\<project_name\>/notebooks/foo/bar/\<notebook_id\>/copy | copies notebook of given ID to "/\<project_name\>/foo/bar" directory |
| ```GET``` | /\<project_name\>/notebooks/foo/bar/new | creates a new notebook with profile (or default, if no profile exists) settings in"/\<project_name\>/foo/bar" |
| ```GET``` | /\<project_name\>/notebooks/foo/bar/\<notebook_id\>/print | prints the notebook with the given ID; if notebook ID doesn't exist, displays error message |
| ```GET``` | /\<project_name\>/edit/foo/bar | go to live edit view for text files |

## See the PR for this IPEP ##

Go to the PR [#3619](https://github.com/ipython/ipython/pull/3619).

## See discussion about this IPEP ##

Go to the IPEP 16 ~ issue #3166 [here](https://github.com/ipython/ipython/issues/3166).

## Web service documentation for this proposal ##

Check out the full documentation for the proposed web service [here](https://gist.github.com/Zsailer/5767194)