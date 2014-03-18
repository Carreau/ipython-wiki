<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Jonathan Frederic &lt;jon.freder@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> October 9, 2013</td></tr>
<tr><td> Updated </td><td> March 18, 2014</td></tr>
<tr><td> Implementation </td><td>  <a href="https://github.com/ipython/ipython/pull/4374">#4374</a> </td></tr>
</table>

# Abstract
Most scientific computing applications live within a hard-coded graphical user interface (GUI).  Typically the GUI is static.  This IPEP discusses the addition of an API that enables the user to generate and manipulate the GUI of the IPython notebook via *Widgets*.  Prior to the addition of *comms* ([[IPEP 21: Widget Messages]]) this was not possible.

# Description  
The Notebook *widget* is introduced in this IPEP.  widgets are GUI elements in the Notebook *front-end* which are automatically synchronized with equivalent representations in the Notebook *back-end*.  The back-end is where user written code in is executed (traditionally Python, but also Julia, R, etc).  The front-end is where the user enters the code to be executed (the web browser).  Using the special magics `%%javascript` and `%%html`, users can write code that is executed on the front-end.  

Internally the widget framework was first implemented to loosely resemble the model, view, controller (MVC) software design pattern, where the view also behaved as the controller.  Later, because of leaky abstractions, it was decided to only allow one view type per model.  This means instead of an IntModel with SliderView and TextView options, there are IntSliderWidget and IntTextWidget.  The model is a stateful container which holds all the data that is syncronized between the front and back ends.  In the back-end the model is just called a Widget.  

Internally in the front-end, the model is called a *model* and the view and controller are combined and are called a *view*.  The [Backbone.js](http://backbonejs.org/) library is used to separate the model from the view.  Backbone.js is used because it is lightweight and has a minimalistic impact on coding style.  In the back-end, IPython traitlets are used to represent the properties of the widget.  A comm is used to syncronize the traitlets between the model (front-end) and widget (back-end).  In addition to traits, events and one-way method calls are sent via the same comm.

# Life cycle  
When a widget is constructed in the back-end, a comm and front-end model are created automatically.  The IPython `display(...)` method is used to display a widget.  Once a widget is displayed, a view is created in the front-end.  The view renders the values stored in the widget's model.  If a view_name is specified, the front-end will create a view of that type instead of the default.  

The comm and model in the front-end will exists until the user closes the widget from the back-end.  When `widget.close()` is called on a widget that has views in the front-end, all of the views are deleted along with the comm and model.  Before a notebook cell is executed, the views displayed within that cell's `widget_subarea` are deleted.

# Views and output
One or more views of a single type can exist for one model in one or more output areas.  A model can be represented by more than one type of view.  Backbone.js will automatically synchronize all of the views with each other and the corresponding model.  In the Notebook, new views are appended to the `widget_subarea` associated with the *executing cell* unless the model is a child of another model, specified by `parent.children = [child]`.  

The executing cell is either the cell that executed the code or the cell associated with the widget that executed the code.  Standard IPython output will always be mapped to the executing cell when available.  The only instance where an executing cell won't exist is when the code is triggered by a widget without any associated views (see Life Cycle section above).  If no executing cell exists, the output will not display.

# Implementing widgets in alternative kernels
This assumes that *comms* ([[IPEP 21: Widget Messages]]) are already implemented.

To create and display a widget:
1. Open a comm with a `target_name` of `WidgetModel`.  The widget manager watches for comms with the `target_name="WidgetModel"` and creates a widget model
when that comm is created.  The comm and widget model are automatically connected.
2. Send a full state update message on the new comm (see the **update**  Back-end to front-end message spec below).  The full state should include the following:
  `msg_throttle` and `_view_name` 
  `_css` and `visible` if a DOM widget view is specified for `_view_name`
  The rest of the of the properties of the corresponding widget, i.e. `disabled`, `value`, `min`, etc...
3. Send a display message on the comm (see the **display**  Back-end to front-end message spec below).
 NOTE: It's important that the messages sent on the comm are in response to an execution message from the front-end or another widget's comm message.  This is required so the widget framework knows what cell triggered the message and can display the widget in the correct location.

# Comm messages
Since the widget architecture is asymetric, the message protocal used by the architecture is also asymetric.  All messages are sent as JSON.  A symetric API is provided on top of the widgets that allow for custom messages to be sent (see **custom** in the message spec below).  Custom messages are used by specific widgets to send events and call methods.  Every message sent has the following form:

```python
{
    method: unicode,
    [...]
}
```

## Back-end to front-end

There are 3 different methods the back-end can send:
- "display"
- "update"
- "custom"

Each method has a different message structure depending on the contents that need to be sent.

**display**  
Display a view for the model.  Optionally contains a parent, the comm id of the widget that the new view should be displayed as a child of.  The message structure follows:

```python
{
    method: 'display',
}
```

**update**  
Update the model's state or a frament of the model's state.  To reduce latency and bandwidth usage, the widget framework only sends the changes made to the widget's state when the information is available.  The message structure follows:

```python
{
    method: 'update',
    state: dict, # Either the entire state or a fragment of it.
}
```

**custom**  
Custom message to be handled by the widget model, view, or any registered listeners.  The message structure follows: 

```python
{
    method: 'custom',
    content: dict,
}
```

### DOMWidget custom messages
The DOMWidget defines two custom messages.

**add_class**  
Add DOM class(es) to an element of the widget's views in the front-end.  Optionally contains a JQuery selector that selects which element(s) the classes will be added too.  The custom message contents structure follows:

```python
{
    msg_type: 'add_class',
    class_list: unicode, # One or more space separated class names.
    selector: unicode, # optional
}
```

**remove_class**  
Remove DOM class(es) to an element of the widget's views in the front-end.  Optionally contains a JQuery selector that selects which element(s) the classes will be added too.  The custom message contents structure follows:

```python
{
    msg_type: 'remove_class',
    class_list: unicode, # One or more space separated class names.
    selector: unicode, # optional
}
```
## Front-end to back-end messages

There are 2 different methods that the front-end can send:
- backbone
- custom

**backbone**  
Backbone.js initiated sync.  Update the widget's state or a fragment of the widget's state.  To reduce latency and bandwidth usage, the widget framework only sends the changes made to the model's state when the information is available.  The message structure follows:

```python
{
    method: 'backbone',
    sync_data: dict, # State of frament of state.
}
```

**custom**  
Custom message to be handled by the widget or any registered listeners.  The message structure follows:

```python
{
    method: 'custom',
    content: dict,
}
```

# Message throttling
To prevent the kernel from flooding, the messages from the front-end to the back-end must be throttled.  Each model is individually throttled.  If three (by default) messages were sent and all are still processing, the front-end will not send anymore state messages.  Consequtive state messages will be buffered.  Once one of the three pending messages finished processing, the state buffer will be flushed.  The number of messages allowed can be configured using the "msg_throttle" property of the model.

# Examples
For detailed examples, please see `ipython\examples\widgets` in <a href="https://github.com/ipython/ipython/pull/4374">PR #4374</a>.