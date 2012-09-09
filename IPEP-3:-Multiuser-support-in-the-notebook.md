# Overview

The IPython Notebook is currently a single user web application.  We are planning to add multiuser capabilities to the Notebook and the purpose of this IPEP is to work out the design.

This document is very much a rough draft of the design and we encourage people to edit and refine the document as the discussion on ipython-dev proceeds.

Here is a rough sketch of the main design points:

# Users

* Notebook users will be mapped onto local users on the server. Each Notebook user will have a local user account with a home directory and the ability to run programs as that user.
* We will use the Unix security model for isolating users. From a security perspective, Notebooks and kernels are completely equivalent to shell access.  This is deliberate and we will make no attempt to hide this. This means that in a multiuser context all users must be trusted. Securing the Notebook against potentially hostile users is not possible without a multi-layer, system-wide approach.
* User authentication will be handled using PAM.

# Notebooks, kernels and filesystem

* Notebooks will be stored as regular files in the user's home directory and subdirectories.
* The Notebook UI will include the ability to browse notebooks in different directories.
* We will not implement a full featured file system explorer UI. Instead users will have access to a simple shell widget in the Notebook UI where they can issue shell commands to manage directories and files.
* Kernels will be started as processes of the local user. They will have CWD that matches the location of the associated notebook.
* The location of a user's notebooks will be stored in a database and there will be a UUID for each notebook that allows us to track the notebook as it is moved around and renamed.

# Version control and sharing

* Version control of notebooks will be done using the command line interface to git and other VCSs. These VCS command will be issued through the shell widget.
* We will strongly encourage users to share notebooks by cloning each other's git repos.
* By default kernels and notebooks will not be shared with other users.
* Users will have the ability to "publish" notebooks to other users. This will allow the other users to download the notebook in different formats.
* Users will have the ability to "live share" notebooks and their kernels. When a user clicks "live share" they will get a private and secure URL they can share with other users. These other users can then edit the notebook and run code on the kernel in a Google Docs like manner.
* Users collaborating in live share mode should be able to chat with each other.

# Sharing and live editing, fine grained Privileges

We know that social web is dying, and we wish long live to collaborative web,
but especially in our case with dynamic and executable content, collaborative
might have different meaning. This is the reason why we should have different
level of permission when collaborating on a notebook.

Considering a notebook server, a specific notebook and it associated kernel, we
will consider the notebook **owner** as the user which own the kernel, and the
collaborators, as any other users with access to the notebook server.

The notebook owner should be able to give independently the following right to
different users at almost any time.

* Ability to see a static version of the current saved, or specific version of the notebook (cf nbviewer).
* Ability to see a live edited version of the notebook, without being able to interact.
* Ability to edit part of the notebook and push the changes
* Ability to save the current changes on disk
* Ability to execute code on the kernel.

This can be either strictly implementing with strong security, but a soft way to
prevent mistakes is a first step as long as users are warned that by sharing
their notebook, they share a shell on their computer

# Security discussion around the notebook.

This will not focus on server-side security to isolate every user from each
other and the system, but more on what should be keepped in mind when
considering the fact that user might exchange, view, or collaborate on .ipynb
files.

## Attacker have no kernel execution right.

Most of the case we should be concern in, is where the Owner, (or any
collaborator with kernel execution right) with  of a notebook and kernel is a
target of a malicious attack,  if those are prevented, then, this should
automatically protect against the other case.

### Principal vector of attack.

JS injection, cross-site-scripting.

Notebook have the ability to embed script, either in markdown cell, or on code
cell output. Even if those script are not executed on load time, they could be
triggered on link hoovering or other action.

When live collaborating, script can be injecting at any time when the notebook
syncs between different users.

### Principal type of attack

* executing arbitrary code to the kernel,
* execute any command on user account.
* steel credential.

### Protections :

The two main protection are iframe sandboxing (not supported by all browser
yet), where we sandbox javascript into it's iframe.  iframe could be added on
outputarea/rendered-html level, or higher, directly wrapping worksheet or all
notebook. It should prevent inner-frame javascript to have access to outside
javascript.  Ability to outside-frame javascript to bind to inner-frame event
have to be evaluated.

This would need to design a specific way to publish javascript (probably by
going through the kernel), that have access to the all ipython javascript.


Second protection would be to process all sensible area with regular
expression, to prevent binding javascript execution to event, or removing
javascript.

Both approach can be used as the same time.

## Owner is malicious

If owner, or any user with kernel access is malicious, the he can trivially
execute arbitrary code on other clients side.  We are not concern about
privileges escalation here, but with credential steeling.

This could be prevented either by iframe sandboxing (I think) where the
user administrative action are outside of the iframe, and kernel-notebook
related inside the iframe.  This might require 1 cookie per notebook to
separate privileges, but tis is IMHO the most complicated case to solve.

## Supplementary security

One extra security measure that could be added is to enforce a strict checking
of the exchanges json format.  The only place difficult to check is the
metadata that has no strict format.

