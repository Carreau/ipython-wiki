<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Jonathan Frederic &lt;jdfreder@calpoly.edu&gt;</td></tr>
<tr><td> Created </td><td> September 24, 2014</td></tr>
<tr><td> Updated </td><td> September 24, 2014</td></tr>
<tr><td> Discussion </td><td> n/a (link to the issue where the IPEP is being discussed) </td></tr>
<tr><td> Implementation </td><td> n/a </td></tr>
</table>

# Main goals

* Ability to interleave widgets and output.
* Multiple outputs that are addressable (display/print/clear_output)
from Python and can be put into widget hierarchies.
* Kernel authors shouldn't have to implement the comm/widget stuff to
get output to work.
* No additional coupling of the front-end to the kernel that we already
have (through widgets)

## Notebook UI

* There will be one single widget area and no separate output area.
This will allow us to iframe the whole thing easily if we need to go
that way.
* That widget area is a linear sequence of widgets that are displayed
by calling display(w) from Python.
* From the UI perspective output will become a widget that can be put
anywhere in the widget hierarchy.
* When regular output (stdout, display_data, etc.) arrives in the
front-end, we will create a "default output widget" in the next slot in
the widget area. This output widget will not use the Comm layer but
will get its output through an adapter layer between the existing part
of the message spec and the output widget. We will need a Comm-less
widget abstraction for this.

## Python API

* The Python API for regular output (stream, display_data, etc.) will
be exactly the same as today.
* We will create a new Output widget. To direct output to this output
widget, a user will use it as a context manager:

o = Output()
h = HBox([o])
with o:
    print("Hi there")
    display(HTML("<b>WOW</b>"))

* The default output widget won't be available as a Python side
widget. To write output to it a user just makes regular output calls
(print, display) without a context manager.
* The Output widget will have methods like display and clear_output as
well, but the global functions for those will work with the context
managers.

## Notebook Document

* The outputs field per cell will be renamed "widgets". It will be a
linear sequence of widget data.
* The widget data can be of two types:

1. by reference = this widget data is for widget models that we want
to make sure don't get stored multiple times in a nb and which
auto-synchronize multiple views. Our existing widgets will be of this
type. The widget data for these models is stored in a global notebook
widget store by their uuid, which is put into the wigets list for the
cell in which the widget is viewed.
2. by copy = this widget data is for widget models that are stored
inline in the widgets list - if they appear multiple times, they are
copied. Output widgets will be of this type. This will keep regular
output inline in the per cell widget list.

The widget data for either type is built by calling the get_state
method of the widget object in Javascript. Widget and output
persistence will work by calling set_state with the widget data in the
notebook.

## Message spec

No changes to the message spec are needed.