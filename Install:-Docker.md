## IPython Docker Configurations

 Docker Hub: **https://registry.hub.docker.com/u/ipython/**

 Source: https://github.com/ipython/docker-notebook

 OS: Ubuntu 14.04 Trusty Tahr

 Scipy Stack: http://www.scipy.org/stackspec.html

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

## Anaconda + IPython Configurations

* Anaconda + IPython [Notebook]

    ``docker run -it continuumio/anaconda#3``  # NumPy, SciPy

  * https://registry.hub.docker.com/u/continuumio/anaconda/
    * https://github.com/ContinuumIO/docker-images
      * OS: Debian 7.4
      * https://github.com/ContinuumIO/docker-images/blob/master/anaconda/Dockerfile
      * https://github.com/ContinuumIO/docker-images/blob/master/anaconda3/Dockerfile
    * Anaconda Scipy Stack Package Versions: http://docs.continuum.io/anaconda/pkg-docs.html

* Anaconda + IPython Notebook

  ``docker run -p 8888:8888 -i -t rothnic/anaconda-notebook``

  * https://registry.hub.docker.com/u/rothnic/anaconda-notebook/
    * https://github.com/rothnic/anaconda-notebook
      * OS: Debian Jessie
      * https://github.com/rothnic/anaconda-notebook/blob/master/Dockerfile

## Links to Jupyter Hub
* https://registry.hub.docker.com/u/jupyter/
  * https://github.com/jupyter/jupyterhub/
    * https://registry.hub.docker.com/u/jupyter/jupyterhub
      * https://github.com/jupyter/jupyterhub/blob/master/Dockerfile
      * https://github.com/jupyter/notebook/blob/master/Dockerfile
      * **https://github.com/jupyter/jupyterhub/wiki/Spawners**
        * https://developer.rackspace.com/blog/deploying-jupyterhub-for-education/
          * ![Deploying JupyterHub For Education](https://c2a32ff18d23c8f567f0-e44b0df73868b5d567b1e58e01681d15.ssl.cf5.rackcdn.com/2015-03-24-deploying-jupyterhub-for-education/setup-2e74d935ee0c874e66a9b53359493ceb.png)

## Notes regarding use of IPython and Docker
* [ ] TODO: mount a local filesytem directory for notebooks
* [ ] TODO: (jupyter) (GDrive) storage plugins
* [ ] TODO: about running an init process w/ syslog etc (e.g. phusion/baseimage-docker)
* [ ] TODO: sysv/upstart/systemd/supervisord/runit config for IPython Notebook