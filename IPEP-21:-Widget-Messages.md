<table>
<tr><td> Status </td><td> Implemented </td></tr>
<tr><td> Author </td><td> Min RK &lt;benjaminrk@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> September 11, 2013</td></tr>
<tr><td> Updated </td><td> September 25, 2013</td></tr>
</table>

Adding communication for use in interactive widgets.
Note that this does not specify anything about widgets themselves,
it only creates the basic plumbing for communication.

IPython has a message specification for the Frontend (Javascript, etc.)
to communicate with the Kernel and vice versa.
We want developers to be able to build their own interactive tools on top of IPython
by providing an API for creating a pair of objects - one Kernel-side, one Frontend-side -
and facilitating their communication, and a messaging specification, so that these can be implemented in Kernels other than the one provided in IPython.

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
      'target_name' : 'some_key',
      'data' : {}
    }

When one of these messages is received, the recipient should open a new Comm with matching `comm_id`.
The `target_name` key is used to trigger some event when the Comm is opened,
such as instantiating a peer Widget.
If comm creation fails, a `comm_close` message should be sent,
because no Comm should exist without its peer.

Every Comm has a unique ID, shared only with its counterpart.
The `data` key can be any extra JSON information used in initialization of the Comm.

### Sending messages with Comms

This is the primary method for peers to communicate.
Comm messages are one-way communications, which can be used to update state,
either on the Kernel or the frontend side. Both sides can send these messages to the other.

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

## Display Logic

Since these messages allow the execution of arbitrary third party code,
they should have some similarity in their context to the handling of executions.
In particular:

- When entering a `comm_` handler, a `busy` status message should be published.
- When finishing a `comm_` handler, an `idle` status message should be published.
- Display messages sent as a result of the `comm_msg` should have the `comm_msg` header
  as the parent.

# Python and Javascript Implementations

This includes specification for the API of the Python and Javascript implementations of these messages.
It is recommended that any Kernel implementation follow suit as appropriate for the language,
but only the message specification is necessary.

Both the Javascript and Python sides have two basic objects:

1. Comm
2. CommManager

## Comm

There will be a base class for Comms on both sides.
Each Comm instance has a `comm_id`, and a counterpart on the other side.
When an object opens a Comm, it should pass a `target_name` argument,
a string key used to identify the handler for the other side.

Each Comm has six primary methods:

- `open(data?)`, which sends a `comm_open` message
- `send(data)`, which sends a `comm_msg` message
- `close(data?)`, which sends a `comm_close` message
- `on_msg(callback)`, register a callback for when a `comm_msg` message arrives
- `on_close(callback)`, register a callback for when a `comm_close` message arrives

Note that the callbacks you register will receive the **full message**,
while the sending methods only accept the data key,
allowing the Comm object to construct the rest of the message.

To register a callback for handling messages in Python:

Python:
```Python
comm.on_msg(callback)
```

or Javascript:
```Javascript
comm.on_msg(callback);
// or to bind a method as a callback
comm.on_msg($.proxy(obj.callback_method, obj));
```

This is the basic encapsulation of communication between the Frontend and the Kernel.

On the Javascript side, `comm.send` accepts callbacks, just like `kernel.execute`,
which allows dispatch of display messages:

```Javascript
comm.send({key: value}, {output: $.proxy(some_output_area.handle_output, some_output_area)});
```


**Question:** Should open/close get a `data` key? When I have implemented these,
open and close are called by `__init__` and `__del__`, which means there is no easy mechanism
to pass arguments to open and close (other than private attributes).


### CommManager

The CommManager maintains a mapping of comm_id to Comm instance.
To add a comm to the mapping, use:

    comm_id = CommManager.register_comm(comm)

CommManagers also maintain a mapping of `target_name` to functions,
used for handling the creation of user objects attached to the new comm.
Typically, these would be constructors for Widget counterparts.
In general, a comm that is created directly by user code is called **primary**,
and its counterpart whose creation is triggered by a `comm_open` message
is called **secondary**.

The target function will be passed two arguments: the connected Comm instance,
and the `comm_open` message that opened the Comm.

To register a target function, use:

    CommManager.register_target('target_name', MyCommClass)

This mechanism determines what objects can be created from the other side via secondary comms,
and functions as a whitelist.

For instance, a Javascript plugin that defines a new widget will need to call this before
anything can be hooked up:

    IPython.comm_manager.register_target('mywidget', my_js_func);

The IPython Kernel equivalent where the Kernel-side object is secondary would be:

    get_ipython().comm_manager.register_target('mywidget', MyWidget)
    
Where MyWidget is just a class, whose constructor takes exactly two arguments.

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

Each Widget HAS A Comm. Comms are simple and generic - when you develop a Widget,
it should HAVE A Comm, and use it for communicating with its counterpart on the other side.
Comms are not subclassed.

## Caveats

Since we do not yet have a mechanism for storing this kind of state in the notebook document,
reloading the page will close the Javascript-side instances of all comms,
but the Kernel-side instances will all remain without their peers.
Restarting the Kernel does the same, in reverse.
Re-sending `comm_open` messages for all extant Comms should re-establish connections,
so perhaps there should be an API call on either side that does this.

Since this is inherently reply-less and asynchronous, 
one pattern that may be desirable that will not be easily implemented with this architecture
is the Kernel asking the Frontend a question and waiting for the response.
For instance, `raw_input` could not easily be implemented with this architecture,
because if the Python call is blocking waiting for a reply,
the reply will never come because it is blocking the handler that will receive the reply.
