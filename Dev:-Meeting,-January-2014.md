- reviewing widgets stuff  
    Design questions:  
    - `get_msg_cell`, `get_cell` callback - should this callback exist for all IPython notebook messages?  
    - How do we want to use require as it relates to our main.js and the IPython namespace?
    - Do width and height attribute for all of the widgets?  Should they use css?
    - Widget binding to other widgets?
    - Do we want to expose flexible box model to users in Python?

- notebook storage & contents webservice
- nbviewer future plans
- discovery of running notebook servers
- kernel registry - and separating config for servers/users/kernels/notebooks

## Design of multiuser server

- pluggable authentication
- unix user model: what parts of collaboration fit well with this, which ones don't?
- concept of projects? Is that the sharing unit?
- separate codebase for server vs. current server code. 
  * What can be reused? 
  * Does the multiuser server start single-user ones?