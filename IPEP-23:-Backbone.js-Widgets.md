<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Jonathan Frederic &lt;jon.freder@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> October 9, 2013</td></tr>
<tr><td> Updated </td><td> December 3, 2013</td></tr>
<tr><td> Implementation </td><td>  <a href="https://github.com/ipython/ipython/pull/4374">#4374</a> </td></tr>
</table>



-----

**! This document is incomplete !** 

Missing:
- Usage examples  
- Throttling description  
- Message diff details  

-----

# Abstract
Most scientific computing applications live within a hard-coded graphical user interface (GUI).  Typically the GUI is static.  This IPEP discusses the addition of an API that enables the user to generate and manipulate the GUI of the IPython notebook via *Widgets*.  Prior to the addition of *comms* ([[IPEP 21: Widget Messages]]) this was not possible.

# Description  
The Notebook *widget* is introduced in this IPEP.  widgets are GUI elements in the Notebook *front-end* which are automatically synchronized with equivalent representations in the Notebook *back-end*.  The back-end is where user written code in is executed (traditionally Python, but also Julia, R, etc).  The front-end is where the user enters the code to be executed (the web browser).  Using the special magics `%%javascript` and `%%html`, users can write code that is executed on the front-end.  

The widget framework was implemented to loosely resemble the *model*, view, controller (MVC) software design pattern.  The model is the data that needs to be syncronized between the front and back ends.  In the back-end, only the model exists.  The model in the back-end is what is called a *Widget*.  In the front-end, the model is called a *model* and the view and controller are combined and are called a *view*.

In the front-end, the [Backbone.js](http://backbonejs.org/) library is used to separate the model from the view.  Backbone.js is used because it is lightweight and has a minimalistic impact on coding style.  In the back-end, IPython traitlets are used to represent the properties of the widget.  A comm is used to syncronize the traitlets between the model (front-end) and widget (back-end).  In addition to traits, events and one-way method calls are sent via the same comm.

# Life cycle  
When a widget is constructed in the back-end, a comm and front-end model **are not** created immediately.  Widgets can exist in the back-end without the fron-end knowing of their existance.  The IPython `display(...)` method is used to display a widget.  Once a widget is displayed, a comm is created and a model is created in the front-end.  If a view_name is specified or a default exists, the front-end will also create a front-end view attached to the model.  If the view type can't be found by name, no view will be created.  If no view is created, the model, comm, and widget will all exist until the user calls `widget.close()` or the garbage collect (GC) collects the widget.

If a view was displayed, the comm for a widget will exists as long as at least one view exists in the front-end.  Before a notebook cell is executed, the views displayed within that cell's `widget_subarea` are deleted.  When all of the views in the front-end are closed, the comm is also closed and the model is deleted.  When the user `displays` the widget again, a new comm and model are created.  When `widget.close()` is called on a widget that has views in the front-end, all of the views are deleted along with the comm and model.

# Views and output
One or more views can exist for one model in one or more output areas.  A model can be represented by more than one type of view.  Backbone.js will automatically syncronize all of the views with eachother and the corresponding model.  In the Notebook, new views are appended to the `widget_subarea` associated with the *executing cell* unless specified using the optional `display(..., parent=)` keyword argument.  The executing cell is either the cell that executed the code or the cell associated with the widget that executed the code.  Standard IPython output will always be mapped to the executing cell when available.  The only instance where an executing cell won't exist is when the code is triggered by a widget without any associated views (see Life Cycle section above).  If no executing cell exists, the output will not display.

# Comm messages
Since the widget architecture is asymetric, the message protocal used by the architecture is also asymetric.  All messages are sent as JSON.  A symetric API is provided on top of the widgets that allow for custom messages to be sent (see **custom** in the message spec below).  Custom messages are used by specific widgets to send events and call methods.  Every message sent has the following form:

```python
{
    method: unicode,
    [...]
}
```

## Back-end to front-end

There are 5 different methods the back-end can send:
- "display"
- "update"
- "add_class"
- "remove_class"
- "custom"

Each method has a different message structure depending on the contents that need to be sent.

**display**  
Display a view for the model.  Optionally contains a parent, the comm id of the widget that the new view should be displayed as a child of.  The message structure follows:

```python
{
    method: 'display',
    view_name: unicode,
    parent: unicode, # optional
}
```

**update**  
Update the model's state or a frament of the model's state.  The message structure follows:

```python
{
    method: 'update',
    state: dict, # Either the entire state or a fragment of it.
}
```

**add_class**  
Add DOM class(es) to an element of the widget's views in the front-end.  Optionally contains a JQuery selector that selects which element(s) the classes will be added too.  The message structure follows:

```python
{
    method: 'add_class',
    class_list: unicode, # One or more space separated class names.
    selector: unicode, # optional
}
```

**remove_class**  
Remove DOM class(es) to an element of the widget's views in the front-end.  Optionally contains a JQuery selector that selects which element(s) the classes will be added too.  The message structure follows:

```python
{
    method: 'remove_class',
    class_list: unicode, # One or more space separated class names.
    selector: unicode, # optional
}
```

**custom**  
Custom message to be handled by the widget or any registered listeners.  The message structure follows:

```python
{
    method: 'custom',
    custom_content: dict,
}
```

## Front-end to back-end messages

There are 2 different methods that the front-end can send:
- backbone
- custom

**backbone**  
Backbone.js initiated sync.  Update the widget's state or a fragment of the widget's state.  The message structure follows:

```python
{
    method: 'backbone',
    sync_method: unicode, # CREATE, PATCH, or UPDATE - see Backbone.js documentation.
    sync_data: dict, # State of frament of state.
}
```

**custom**  
Custom message to be handled by the widget or any registered listeners.  The message structure follows:

```python
{
    method: 'custom',
    custom_content: dict,
}
```
