# DRAFT

# Introduction

The IPython spec allows for a very restricted set of messages, and particularly one for executing a string of code.  This requires that for any communication to change the state of the user namespace on the server, or to perform a computation and receive output, the client must form a valid python string of code.  This is error-prone and ignores the richer facilities we have for passing data back and forth between the client and the server.

Another way to look at it is: the IPython server has the ability to pass rich data (i.e., JSON) to the client, but the client can only pass strings back to the server to execute.  This IPEP provides a way for the client to pass JSON back to a custom message handler on the server.

# Discussion items

## Registration

Since the kernel dispatches messages, the kernel is the place to add a function for dispatching custom messages to a custom message handler, based on the message type.  A new method should be added to the shell object returned from `get_ipython()` which registers a handler with the kernel.  This seems a bit klunky, given that the shell doesn't really reference the kernel currently.  Alternatively, a new function can be injected into the user space somehow directly by the kernel to register a method handler.

## Message types

Here are two ideas for dealing with how to encode message types:

* Make one `custom` message type that is added to the spec, and then the message handler is chosen based on a new subtype field, or on a `msg_type` attribute in the content dictionary.  This preserves the top-level message type namespace by just nesting custom messages into the content field.  It also nicely isolates the user code from the routing information in the message header.  However, it might be wasteful to basically embed messages inside of other messages
* Insist that any message types registered use some sort of namespace-like name (e.g., `matplotlib.image_update`).  The top-level namespace is preserved for the IPython message spec.  This puts the IPython core messages on the same level as custom message handlers.
* Do we insist that messages from the client to the server end in `_request`, like the IPython ones do?
* If a custom message handler is registered, say `matplotlib.update`, then should a `matplotlib.update_reply` message type automatically be created for a return message (and return status of the handler, etc.)?  This would mirror the `execute_reply` message.

## Handler signature

I see little reason to provide a message handler with more than the content and the metadata dictionaries.

## Execution

* Should messages be passed through the input splitter and transformation system?  I think probably not, since this custom message handling is designed for low-level communication between the client and server.
* Custom message handlers should behave as though the code was executed with `silent=True` and `store_history=False`
* Custom message handlers should not broadcast the fact that they are executing code to all clients (the `silent=True` might imply this)
* Error handling, setting the stdout and stderr and display parent messages, etc., should follow the conventions of the `execute_request` handling.  This functionality should be abstracted out to a context manager.

# Previous work

Jason Grout has some experiments he has been doing with custom messages and implementing Sage interacts.

Brian Granger has worked on adding custom message handlers for a javascript client: https://github.com/ipython/ipython/pull/2518.  Some discussion about server-side custom message handlers happened on that ticket.

Jason Grout submitted a (now closed) pull request for custom message handlers: https://github.com/ipython/ipython/pull/1700.  A lot of discussion there informed some of the options presented in this IPEP.