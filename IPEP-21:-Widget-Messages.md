<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Min RK &lt;benjaminrk@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> September 11, 2013</td></tr>
<tr><td> Updated </td><td> September 12, 2013</td></tr>
</table>

Adding communication for use in interactive widgets.

IPython has a message specification for the Frontend (Javascript, etc.)
to communicate with the Kernel and vice versa.
We want developers to be able to build their own interactive tools on top of IPython
by providing an API for creating a pair of objects - one Kernel-side, one Frontend-side -
and facilitating their communication, and a messaging specification, so that these can be implemented in kernels other than the one provided in IPython.

This proposal includes message types for dispatching messages to particular communicators,
each of which has a unique ID. This should allow developers to set up communication
between their own Kernel and Frontend-side objects.

## Messages

In general, these messages are one-way with no reply.
Kernels send messages to frontends on IOPub, and frontends send messages to Kernels on Shell.
All new messages have a `data` key, which is any JSONable dictionary.
Essentially, this dictionary allows any widget developer to define their own message specification
for their objects to communicate with each other, without risk of message type or API collision.

### Comm Creation

Creating a communication pipe can be done on either side,
and doing so produces produces a `comm_open` message.

    {
      'comm_id' : 'u-u-i-d',
      'target' : 'some_key',
      'data' : {}
    }

When one of these messages is received, the recipient should create a new Comm with matching `comm_id`.
The `target` key is used to trigger some event when the Comm is created,
such as instantiating a peer Widget.
If comm creation fails, a `comm_close` message should be sent,
because no Comm should exist without its peer.

Every Comm has a unique ID, shared only with its counterpart.
The `data` key can be any extra JSON information used in initialization of the widget.

### Sending messages with Comms

This is the primary method for peers to communicate.
Comm messages are one-way communications, which can be used to update widget state,
either on the Kernel or the frontend side. Both sides can send these messages.

The kernel sends these messages on the IOPub channel, and the frontend sends them on the Shell channel.
There are no expected replies (of course, one side can send another `comm_msg` in reply).

Message type: `comm_msg`:

    {
      'comm_id' : 'u-u-i-d',
      'data' : {}
    }

### Tearing Down Comms

Since Comms live on both sides, when one comm is closed the other side must be notified.
This is done with a `comm_close` message,
which can come from either side via IOPub or Shell.

Message type: `comm_close`:

    {
      'comm_id' : 'u-u-i-d',
      'data' : {}
    }

# Python and Javascript Implementations

This includes specification for the API of the Python and Javascript implementations of this message specification.
It is recommended that any Kernel implementation follow suit as appropriate for the language,
but only the message specification is necessary.

Both the Javascript and Python sides have two basic objects:

1. Comm
2. CommManager

## Comm

There will be a base class for Comms on both sides.
Each Comm instance has a `comm_id`, and a counterpart on the other side.
When an object creates a Comm, it should pass a `target` argument,
a string key used to identify the handler for the other side.

Each widget has six primary methods:

- `open(data?)`, which sends a `comm_open` message
- `send(data)`, which sends a `widget_msg` message
- `close(data?)`, which sends a `widget_close` message
- `handle_open(data)`, called when a `comm_open` message arrives
- `handle_msg(data)`, called when a `widget_msg` message arrives
- `handle_close(data)`, called when a `widget_close` message arrives

The `handle_foo` methods trigger callbacks, registered via methods:

Python:
```Python
comm.on_msg(callback)
```

or Javascript:
```Javascript
$([comm]).on("comm_msg", callback);
```

This is the basic encapsulation of communication.

**Question:** the handle_foo messages get the `data` dict, not the full message.
This means they don't have access to metadata, etc.  Should the handlers get the full message instead?

**Another Question:** Should open/close get a `data` key? When I have implemented these,
open and close are called by `__init__` and `__del__`, which means there is no easy mechanism
to pass arguments to open and close (other than private attributes).


### CommManager

The CommManager maintains a mapping of comm_id to Comm instance.
To add a comm to the mapping, use:

    comm_id = CommManager.register_comm(comm)

CommManagers also maintain a mapping of `target` to callbacks,
for handling the creation of a new comm.
Typically, these would be constructors for Comm objects.
The callback will be passed only one argument: the connected Comm instance.
`Comm.handle_open()` will be called after the handler,
so event listeners for on_open registered by the callback will be called.

To register a callback with a target key, use:

    CommManager.register_target('target', MyCommClass)

This mechanism determines what objects can be created from the other side,
and functions as a whitelist.

For instance, a Javascript plugin that defines a new comm will need to call this before
any comms can be instantiated:

    IPython.comm_manager.register_target('mycomm', my_js_func);

The IPython Kernel equivalent is

    get_ipython().comm_manager.register_target('mycomm', MyPythonComm)

#### handlers

The CommManager has a method for each message type registered as a handler
(`CommManager.comm_msg(msg)`, etc.).
The Kernel-side manager registers them on the Shell channel,
and the Frontend manager registers them on the IOPub channel.

Any time a `comm` message arrives, it is dispatched to the appropriate Comm instance
in the mapping, identified by the `comm_id` key in the message content.
The Comm's `handle_foo` method then dispatches the `data` dict from the message to the registered callback,
if any.


## Creating Comms

In IPython, instantiating a Comm automatically registers the instance with the CommManager,
and sends the `comm_open` message on the IOPub channel.
So as soon as you have a Comm instance, it is ready to use.

## Caveats

Since we do not have a mechanism for storing this kind of state in the notebook document,
reloading the page will close the Javascript-side instances of all comms,
but the Kernel-side instances will all remain without their peers.
Restarting the Kernel does the same, in reverse.
Re-sending `comm_open` messages for all extant Comms should re-establish connections,
so perhaps there should be an API call on either side that does this.
