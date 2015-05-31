## Running a notebook server

http://ipython.org/ipython-doc/3/notebook/public_server.html

## Docker Images for IPython

IPython itself has a [base image on Docker hub, called ipython/ipython](https://registry.hub.docker.com/u/ipython/ipython/):

```
docker pull ipython/ipython
```

As well as [derivative images](https://github.com/ipython/docker-notebook) that contain the scipystack, just a notebook server, and the notebook server+scipystack.

## Deploying IPython notebook

[Chef Cookbook for the IPython Notebook](https://github.com/rgbkrk/ipynb-cookbook) - also has simple setup on Vagrant to deploy an IPython notebook via Vagrant.

## IPython Notebook storage on Cloud Object Storage

[Bookstore](https://github.com/rgbkrk/bookstore) saves IPython notebooks to OpenStack Clouds over Swift. Has a built in module to work with public cloud provider Rackspace.

## IPCluster

[Utilities to deploy a IPython parallel cluster on Windows Azure](https://github.com/ogrisel/ipython-azure)

[SaltStack states to setup an IPython cluster](https://github.com/ogrisel/salt-ipcluster)

## Run IPython inside Docker

* https://github.com/mingfang/docker-ipython
* https://registry.hub.docker.com/u/parente/ipython/ - Bare-bones IPython 2.0 notebook server image with HTTPS, optional password, and pip for installing additional packages