<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Min RK &lt;benjaminrk@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> September 11, 2013</td></tr>
<tr><td> Updated </td><td> September 11, 2013</td></tr>
</table>

Adding messages for interactive widgets.

IPython has a message specification for the frontend (Javascript, etc.)
to communicate with the Kernel and vice versa.
We want developers to be able to build their own interactive tools on top of IPython
by providing an API for creating a pair of objects - one Kernel-side, one Frontend-side -
and facilitating their communication, and a messaging specification, so that these can be implemented in kernels other than the one provided in IPython.

## Widget Messages

In general, widget messages are one-way with no reply.
Kernels send messages to frontends on IOPub, and frontends send messages to Kernels on Shell.
All widget messages have a `data` key, which is any JSONable dictionary.
Essentially, this dictionary allows any widget developer to define their own message specification
for their objects to communicate with each other, without risk of message type or API collision.

### Widget Creation

Creating a widget is always done on the Kernel side.
Creating a widget produces a `widget_create` message on the IOPub channel:

    {
      'widget_id' : 'u-u-i-d',
      'widget_type' : 'my_widget',
      'data' : {}
    }

Every widget has an ID and a type identifier (class).
The `data` key can be any extra JSON information used in initialization of the widget.

### Updating Widgets

This is the primary method for widget peers to communicate.
Widget updates are one-way communications to update widget state, either
on the Kernel or the frontend side. Both sides can send these messages.

The kernel sends these messages on the IOPub channel, and the frontend sends them on the Shell channel.
There are no expected replies (of course, one side can send another `widget_update` in reply).

Message type: `widget_update`:

    {
      'widget_id' : 'u-u-i-d',
      'data' : {}
    }

### Tearing Down Widgets

Since widgets live on both sides, when a widget is destroyed the other side must be notified.
This is done with a `widget_destroy` message,
which can come from either side via IOPub or Shell.

Message type: `widget_destroy`:

    {
      'widget_id' : 'u-u-i-d',
      'data' : {}
    }

# Python and Javascript Implementations

This includes specification for the API of the Python and Javascript implementations of this message specification.
It is recommended that any Kernel implementation follow suit as appropriate for the language,
but only the message specification is necessary.

Both the Javascript and Python sides have two basic objects:

1. Widget
2. WidgetManager

## Widget

There will be a base class for Widgets on both sides.
Each widget has a `widget_id`, and a counterpart on the other side.
Each widget class has a `widget_type`,
which is a string key used to identify the constructor for the frontend counterpart.

Each widget has four primary methods:

- `create()`, which sends a `widget_create` message
- `update(data)`, which sends a `widget_update` message
- `destroy()`, which sends a `widget_destroy` message
- `handle_create(data)`, called when a `widget_create` message arrives
- `handle_update(data)`, called when a `widget_update` message arrives
- `handle_destroy(data)`, called when a `widget_destroy` message arrives

This is the basic encapsulation of widget communication.

**Question:** the handle_foo messages get the `data` dict, not the full message.
This means they don't have access to metadata, etc.  Should the handlers get the full message instead?

### WidgetManager

The WidgetManager maintains a mapping of widget_id to Widget instance.
To add a widget to the mapping, use:

    widget_id = WidgetManager.register_widget(widget)

WidgetManagers also maintain a mapping of `widget_type` to constructor.
These are used to create counterparts (on both sides),
as a part of handling `widget_create` messages.
To register a Widget class with a type key, use:

    WidgetManager.register_widget_type('widget_type', WidgetClass)

This mechanism determines what widgets can be created,
and is used to implement a whitelist of 

For instance, a Javascript plugin that defines a new widget will need to call this before
any widgets can be instantiated.

    IPython.widget_manager.register_widget_type('mywidget', MyJSWidget)

The IPython Kernel equivalent is

    get_ipython().widget_manager.register_widget_type('mywidget', MyPyWidget)

#### handlers

The WidgetManager has a method for each message type registered as a handler
(`WidgetManager.widget_update(msg)`, etc.).
The Kernel-side manager registers them on the Shell channel,
and the Frontend manager registers them on the IOPub channel.

Any time a widget message arrives, it is dispatched to the appropriate widget instance
in the widget mapping, identified by the `widget_id` key in the message content.
The Widget's method is then called with the `data` dict from the message contents.


## Creating Widgets

In IPython, instantiating a widget registers the instance with the WidgetManager,
and sends the `widget_create` message on the IOPub channel.
So as soon as you have a Widget instance, it is ready to use.

## Caveats

Since we do not have a mechanism for storing widget state in the notebook document,
reloading the page will destroy the Javascript-side instances of all widgets,
but the Kernel-side instances will all remain without their peers.
Restarting the Kernel does the same, in reverse.
Re-sending `widget_create` messages for all extant widgets should re-establish connections.
