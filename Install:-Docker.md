## IPython Docker Configurations

IPython Docker Hub Repos: **https://registry.hub.docker.com/u/ipython/**

Source: https://github.com/ipython/docker-notebook

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

## Links

Jupyter / JupyterHub

* https://github.com/jupyter/jupyterhub/
  * https://github.com/jupyter/jupyterhub/blob/master/Dockerfile
  * https://registry.hub.docker.com/u/jupyter/
    * https://registry.hub.docker.com/u/jupyter/jupyterhub
    * https://registry.hub.docker.com/u/jupyter/tmpnb/
      * https://github.com/jupyter/configurable-http-proxy/blob/master/Dockerfile
      * https://github.com/jupyter/tmpnb/blob/master/Dockerfile
      * https://github.com/jupyter/dockerspawner