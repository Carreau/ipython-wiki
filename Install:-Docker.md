## IPython Configurations

A few ways to install IPython with Docker:

* One instance of IPython:

  ``docker run -it ipython/ipython``

* One instance of IPython + SciPy Stack (``ipython/scipystack``):

  ``docker run -it ipython/scipystack``

* One instance of IPython Notebook (``ipython/notebook``):

  ``docker run -d -p 443:8888 -e "PASSWORD=MakeAPassword" ipython/notebook``

  Then access IPython notebook over SSL/TLS at https://localhost

* One instance of IPython Notebook + SciPy Stack (``ipython/scipyserver``):

  ``docker run -d -p 443:8888 -e "PASSWORD=MakeAPassword" ipython/scipyserver``


## Links

* **https://github.com/ipython/ipython/blob/master/Dockerfile**
* https://github.com/ipython/docker-notebook
  * https://github.com/ipython/docker-notebook/blob/master/notebook/Dockerfile
  * https://github.com/ipython/docker-notebook/blob/master/scipystack/Dockerfile
    * https://github.com/ipython/docker-notebook/blob/master/scipystack/build_scipy_stack.sh
    * https://github.com/ipython/docker-notebook/blob/master/scipyserver/Dockerfile
  * https://registry.hub.docker.com/u/ipython/
    * https://registry.hub.docker.com/u/ipython/ipython
    * https://registry.hub.docker.com/u/ipython/scipyserver/
* https://github.com/jupyter/jupyterhub/
  * https://github.com/jupyter/jupyterhub/blob/master/Dockerfile
  * https://registry.hub.docker.com/u/jupyter/
    * https://registry.hub.docker.com/u/jupyter/jupyterhub
    * https://registry.hub.docker.com/u/jupyter/tmpnb/
      * https://github.com/jupyter/configurable-http-proxy/blob/master/Dockerfile
      * https://github.com/jupyter/tmpnb/blob/master/Dockerfile
      * https://github.com/jupyter/dockerspawner