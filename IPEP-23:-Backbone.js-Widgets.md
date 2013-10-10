<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Jonathan Frederic &lt;jon.freder@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> October 9, 2013</td></tr>
<tr><td> Updated </td><td> October 9, 2013</td></tr>
<tr><td> Implementation </td><td>  <a href="https://github.com/ipython/ipython/pull/4374">#4374</a> </td></tr>
</table>

# Abstract
Typical scientific computing suites live within a hard-coded GUI (graphical user interface).  This stereotype can be broken by exposing an API that allows the user to generate and manipulate their GUI.  By adding this functionality to the IPython Notebook, users will be able to define the environment in which their code is executed.  Prior to the addition of comms ([[IPEP 21: Widget Messages]]) this was not possible.

The Notebook Widget is introduced in this IPEP.  Notebook Widgets are GUI elements in the Notebook front-end which are automatically synchronized with equivalent representations in the Notebook back-end.  With the addition of Widgets, basic GUIs can be implemented completely in the back-end.

# Architecture
It’s important to understand that user written code in is generally executed in the back-end (traditionally Python, but also Julia, R, etc).  Using the special magics `%%javascript` and `%%html`, users can write code that is executed on the front-end (in the web browser).  The addition of comms allows for custom communication between front-end and back-end code.  

When designing the Widget architecture, we wanted to make sure that the widgets could be maximally reusable, configurable, and distributable.  It is also important that the widgets themselves are independent of the data they represent.  For this reason we decided to try to model our architecture after the MVC (model, view, and controller) design pattern.  In the MVC pattern, the model contains all of the data, the view is used to render that data, and the controller is used to adjust the model (as seen in Figure 1).  There are many Javascript libraries that aid in the creation of MVC architectures.  We chose the [backbone.js](http://backbonejs.org/) library to implement our architecture because it is lightweight and has a minimalistic impact on coding style. 

<center><table><tr><td><center>[[/File/IPEP-23/MVC.png]]  
</center></td></tr><tr><td><center>*Figure 1: Interaction of MVC components*</center></td></tr></table></center>  

The MVC design pattern has to be altered to work within IPython.  In the architecture introduced by this IPEP, the model, view, controller all live in the front-end.  A copy of the model exists in the back-end, which is automatically synced with the model in the front end (as seen in Figure 2).  

<center><table><tr><td><center>[[/File/IPEP-23/ModelSync.png]]  
</center></td></tr><tr><td><center>*Figure 2: Synchronization of each model in the front-end with the corresponding model in the back-end is done using a comm.*</center></td></tr></table></center>  

The models created with backbone.js are automatically synchronized by backbone.js with a user specified back-end (usally a RESTful server).  Backbone.js uses the `Backbone.Sync(...)` method to perform the syncronization. By overwriting the `Backbone.Sync(...)` function, all of the edits can be sent to the back-end using a comm.  

In the Python back-end (other back-ends discussed later in this IPEP), `traitlets` are used to define each property of the model.  The `traitlets` machinery listens for the properties to change.  When a property is changed, the modified state of the model is sent to the front-end via the same comm. 

The *view* and *controller* are combined in our architecture since widgets both display and accept input.  The combined view and controller are simply referred to as a **view** in our architecture.  The view **only exists in the front-end**.  

This raises the first design question; How can users specify when and how a view should be displayed on the front-end via the back-end?  The solution is a `model.show(...)` method in the back-end.  When the `model.show(...)` method is called, a message is sent to the front-end instructing it to instanciate a view for the model (as seen in Figure 3).
 
<center><table><tr><td><center>[[/File/IPEP-23/Show.png]]  
</center></td></tr><tr><td><center>*Figure 3: Result of calling `model.show(...)`*</center></td></tr></table></center>  

The IPython Notebook is designed so code is executed on a cell by cell basis.  It would not be unusual to desire the same Widget to be available in more than one cell’s output.  By calling `model.show(...)` again, the user can create another view linked to the same model (as seen in Figure 4).

<center><table><tr><td><center>[[/File/IPEP-23/ShowMultiple.png]]  
</center></td></tr><tr><td><center>*Figure 4: Multiple views associated with one model*</center></td></tr></table></center>  

The beauty of this design is that model state is only sent once between the back-end and the front-end via the comm.  The views are synchronized within the front-end (as seen in Figure 5).

<center><table><tr><td><center>[[/File/IPEP-23/Modification.png]]  
</center></td></tr><tr><td><center>*Figure 5: Synchronization of the views to the model occurs completely within the front-end.*</center></td></tr></table></center>  

Models and views are separate entities.  The user can choose what view is used for a model.  The user can even create a custom view for an existing model.  Since multiple views can be displayed for one model, the user can represent a model more than one way in a single cell’s output (as seen in Figure 6).  For example, one could show a slider view and a numeric text box view for the same numeric model.  This would allow the user to slide the slider or enter a value explicitly in the numeric text box.  To choose what view is shown, one must pass the view’s name when calling the `model.show(viewname="myview")` method (replace "myview" with the view's name).

<center><table><tr><td><center>[[/File/IPEP-23/Multiview.png]]  
</center></td></tr><tr><td><center>*Figure 6: Models can be represented with multiple different views.  Users are able to create their own views for existing models.*</center></td></tr></table></center>  

The second major design question arises because of the need to be able to nest views within each other; How can you display one view within another?  Since nesting is only a function of the views, it’s hard to appropriately expose it in the back-end.  The solution is to allow models in the back-end to be child of each other (as seen in Figure 7). 

<center><table><tr><td><center>[[/File/IPEP-23/Parent.png]]  
</center></td></tr><tr><td><center>*Figure 7: Model parent child relationships can exist on the Python side.*</center></td></tr></table></center>  

When the user calls `model.show(...)` and the model has a parent, the id of the parent is sent along with the show message to the front-end.  When a user calls `model.show(...)` and the model has children, the children of that model are also shown (as seen in Figures 8-9).  

<center><table><tr><td><center>[[/File/IPEP-23/ParentShowStep1.png]]  
</center></td></tr><tr><td><center>*Figure 8: First step - parent view is rendered*</center></td></tr></table></center>  

<center><table><tr><td><center>[[/File/IPEP-23/ParentShowStep2.png]]  
</center></td></tr><tr><td><center>*Figure 9: Second step - children views are rendered*</center></td></tr></table></center>  

When the front-end receives the command to show a view for a model that is child to another model, it looks to see if a view exists for the parent.  If the parent view does exist, the child view is created within that parent view.  If a parent doesn't already exist, the view is shown alone (without the parent).  

Even though the child views are children of a parent view, they are still associated with their own models individually and state synchronization occurs between each view and its respective model.  The information from a child view is **never** relayed to a parent view (as seen in Figure 10).

<center><table><tr><td><center>[[/File/IPEP-23/ParentShowStep3.png]]  
</center></td></tr><tr><td><center>*Figure 10: A view always has a single unique model it is linked to, even if it is child to another view.*</center></td></tr></table></center>  

# Usage
TODO

# Other Back-ends
TODO