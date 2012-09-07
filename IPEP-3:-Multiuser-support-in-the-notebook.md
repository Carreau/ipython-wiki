The IPython Notebook is currently a single user web application.  We would like to add multiuser capabilities to the Notebook and the purpose of this IPEP is to work out the design.

This document is very much a rough draft of the design and we encourage people to edit and refine the document as the discussion on ipython-dev proceeds.

Here is a rough sketch of the main design points:

* Notebook users will be mapped onto local users on the server. Each Notebook user will have a local user account with a home directory and the ability to run programs as that user.
* We will use the Unix security model for isolating users. From a security perspective, Notebooks and kernels are completely equivalent to shell access.  This is deliberate and we will make no attempt to hide this. This means that in a multiuser context all users must be trusted. Securing the Notebook against potentially hostile users is not possible without a multi-layer, system-wide approach.
* User authentication will be handled using PAM.
* Notebooks will be stored as regular files in the user's home directory and subdirectories.
* The Notebook UI will include the ability to browse notebooks in different directories.
* We will not implement a full featured file system explorer UI. Instead users will have access to a simple shell widget in the Notebook UI where they can issue shell commands to manage directories and files.
* Version control of notebooks will be done using the command line interface to git and other VCSs. These VCS command will be issued through the shell widget.
* Kernels will be started as processes of the local user. They will have CWD that matches the location of the associated notebook.
* The location of a user's notebooks will be stored in a database and there will be a UUID for each notebook that allows us to track the notebook as it is moved around and renamed.
* By default kernels and notebooks will not be shared with other users.
* We will strongly encourage users to share notebooks by cloning each other's git repos.
* Users will have the ability to "publish" notebooks to other users. This will allow the other users to download the notebook in different formats.
* Users will have the ability to "live share" notebooks and their kernels. When a user clicks "live share" they will get a private and secure URL they can share with other users. These other users can then edit the notebook and run code on the kernel in a Google Docs like manner.
