  
### Introduction

Further extending IPython Qt Console, beyond Qt wrapper to traditional console, we invite you to discuss the idea of enhancing it with additional side pane.

Couple of reasons can led to this idea.
For example, while working in Qt Console user can't (or can't easily) store notes or save the snippet for future reuse, or build idea based on work in current Qt session or extend with work from previous sessions. I usually do this in a separate editor by side.
Another example could be newly introduced concept in IPython Notebook - reusable cells. IMHO that concept is so attractive, that when I go from Notebook to Qt Console I feel like missing something obvious.
Qt GUI sure offers much more...

### Current raw suggestions

**1. Additional Qt Console specific pane, suggested by _Thomas Kluyver_:**
	
> I call it 'sidecar' - a second pane on the Qt console, like the pager, but able to display help, HTML documentation, plots, data - and, as you suggest, store and edit code snippets. In that sense, the Qt console effectively becomes a sort of lightweight IDE, with the focus on the shell rather than the editor. What I envisage would be quite different from the notebook, though.

* Collapsible side pane
* Simple to shuttle code and results between the console and the sidecar
  - Drag and drop?
  - Run buttons for code?
  - This makes it kind of like the clipboard, but visible and editable
* Possible interfaces:
  - Simple: just a text editor component
  - Tabbed
  - Stack: you can push items onto it, pop them off, and reorder them
  - Linked to the console: items in the sidecar correspond to cell where they were created, and they scroll together
* Integration with matplotlib, so you can get the interactive plot interface inside the sidecar
  - I suspect this might be technically difficult, but it can't hurt to dream.

**2. Qt Console pane with Notebook interaction**

Here are some thoughts about it, abstracting without knowledge about Qt Console internals:

* Lightweight IPython Notebook file format reader/writer, basically JSON parser. (Auto-generated scheme from `00_notebook_tour.ipynb`, hopefully to gather all supported elements, as a reference, can be seen here: <http://i.imgur.com/Z0jSc.png>)
 - Could be able to open Notebook files, perhaps restricted only on one (first) worksheet, and without parsing output cells. 
 - Could not overwrite Notebook with output cells or multiple worksheets.  
 - User could be able to type code or markdown in something like editable boxes made simplest as possible. Code boxes could be parsed with pygments (like in main Qt Console pane) and markdown boxes with module responsible for that.  
  
* Same as help side pane (one instance)
 - If this pane is opened, then help will page bellow the code in main Qt Console pane. 
 - `Esc` could close it.   
  
* New menu function
 - in a manner similar to favorites - notebooks could be saved in IPython home folder, then Qt Console menu could list all notebooks from this folder automatically based on file name. 
 - New menu functions to create new, manually open, and save notebooks.  
  
* Selecting code box and being able to launch the code in main Qt Console pane, would be nifty


**3. Other user ideas?**
