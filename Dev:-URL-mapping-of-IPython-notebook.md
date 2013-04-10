# Architecture of IPython notebook's Dashboard #

The tables below show the current RESTful web service architecture implemented in IPython notebook. The listed URL's use the HTTP verbs to return representations of the desired resource.

We are in the process of creating a new dashboard architecture for the IPython notebook, which will allow the user to navigate through multiple directory files to find desired notebooks.

## Current Architecture ##

This chart shows the web-services in the single directory IPython notebook. 

| HTTP verb | URL | Action |
|:---:|:---:|:---|
| ```GET``` | /notebooks | returns list of dicts with each notebook's info |
| ```POST``` | /notebooks | if sending a body, saving that body as a new notebook; if no body, create a new notebooks | 
| ```GET``` | /notebooks/\<notebook_id\> | get JSON data for notebook |
| ```PUT``` | /notebooks/\<notebook_id\> | saves an existing notebook with body data |
| ```DELETE``` | /notebooks/\<notebook_id\> | deletes the notebook with the given ID | 

This chart shows the architecture for the IPython notebook website.

| HTTP verb | URI | Action |
|:---:|:---:|:---|
| ```GET``` | / | navigates user to dashboard of notebooks and clusters |
| ```GET``` | /\<notebook_id\> | go to webpage for that notebook |
| ```GET``` | /new | creates a new notebook with profile (or default, if no profile exists) settings |
| ```GET``` | /\<notebook_id\>/copy | opens a duplicate copy of the notebook with the given ID in a new tab |
| ```GET``` | /\<notebook_id\>/print | prints the notebook with the given ID; if notebook ID doesn't exist, displays error message |
| ```GET``` | /login | navigates to login page; if no user profile is defined, it navigates user to dashboard|
| ```GET``` | /logout | logs out of current profile, and navigates user to login page |


This chart shows the Web services that act on the kernels and clusters.

| HTTP verb | URL | Action |
|:---:|:---:|:---|
| ```GET``` | /kernels | return the list of kernel ID's currently running |
| ```GET``` | /kernels/\<kernel_id\> | --- |
| ```GET``` | /kernels/\<kernel_id\>/\<kernel_action\>| performs action (restart/kill) kernel with given ID |
| ```GET``` | /kernels/\<kernel_id\>/iopub | --- |
| ```GET``` | /kernels/\<kernel_id\>/shell | --- |
| ```GET``` | /rstservice/render | --- |
| ```GET``` | /files/(.*) | --- |
| ```GET``` | /clusters | returns a list of dicts with each cluster's information |
| ```POST``` | /clusters/\<profile_id\>/\<cluster_action\> | performs action (start/stop) on cluster with given profile ID |
| ```GET``` | /clusters/\<profile_id\> | returns the JSON data for cluster with given profile ID | 
