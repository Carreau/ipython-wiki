## IPython Docker Configurations

 Docker Hub: **https://registry.hub.docker.com/u/ipython/**

 Source: https://github.com/ipython/docker-notebook

 OS: Ubuntu 14.04 Trusty Tahr

A few ways to install IPython with Docker:

    >>> itertools.product(['IPython', 'IPython Notebook'], ['', '+ SciPy Stack'])


* IPython:

  ``docker run -it ipython/ipython``

  * https://registry.hub.docker.com/u/ipython/ipython
  * **https://github.com/ipython/ipython/blob/master/Dockerfile**

* IPython + SciPy Stack (``ipython/scipystack``):

  ``docker run -it ipython/scipystack``

  * https://registry.hub.docker.com/u/ipython/scipystack/
    * https://github.com/ipython/docker-notebook/blob/master/scipystack/Dockerfile
      * https://github.com/ipython/docker-notebook/blob/master/scipystack/build_scipy_stack.sh

* IPython Notebook (``ipython/notebook``):

  ``docker run -d -p 443:8888 -e "PASSWORD=MakeAPassword" ipython/notebook``

  Then access IPython notebook over SSL/TLS at https://localhost
  
  * https://registry.hub.docker.com/u/ipython/notebook/
    * https://github.com/ipython/docker-notebook/blob/master/notebook/Dockerfile
  
* IPython Notebook + SciPy Stack (``ipython/scipyserver``):

  ``docker run -d -p 443:8888 -e "PASSWORD=MakeAPassword" ipython/scipyserver``

  * https://registry.hub.docker.com/u/ipython/scipyserver/
      * https://github.com/ipython/docker-notebook/blob/master/scipyserver/Dockerfile

## Anaconda / Miniconda + IPython Configurations

* https://github.com/ContinuumIO/docker-images

  http://docs.continuum.io/anaconda/pkg-docs.html

    ``docker run -it continuumio/anaconda#3``  # NumPy, SciPy

* https://github.com/rothnic/anaconda-notebook

  ``docker run -p 8888:8888 -i -t rothnic/anaconda-notebook``



## Links

Jupyter / JupyterHub

* https://github.com/jupyter/jupyterhub/
  * https://github.com/jupyter/jupyterhub/blob/master/Dockerfile
  * https://registry.hub.docker.com/u/jupyter/
    * https://registry.hub.docker.com/u/jupyter/jupyterhub
      * **https://github.com/jupyter/jupyterhub/wiki/Spawners**

## Notes regarding use of IPython and Docker

* TODO: mount a local filesytem directory for notebooks
* TODO: (jupyter) (GDrive) storage plugins
* TODO: about running an init process w/ syslog etc (e.g. phusion/baseimage-docker)
* TODO: sysv/upstart/systemd/supervisord/runit config for IPython Notebook