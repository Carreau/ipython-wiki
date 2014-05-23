### Motivation

IPython and Bokeh both have object bridges which mirror python objects on the javascript side.  This work doesn't need to be done twice.  In addition creating a full featured standalone object bridge would be of value to the general web development community.  Essentially we could have a [meteor](https://www.meteor.com/) for Python (well, some limitations because you can't just use python code in the browser)

### Specific Enhancements

The main feature we would need to add to the IPython bridge is the ability to keep models outside the IPython kernel.  Users should be able to take a notebook, and publish using nbviewer, and then to convert their notebook into a server they could host on AWS or digital ocean.  Then nbviewer widgets would be live.  Doing so means extending the IPython python/js bridge to be able to store models to some persistent store, and load them into a webserver and perform interactions during the request/response cycle (or during websocket communications)
