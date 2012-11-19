This document proposes development of a system that would allow the IPython Notebook to utilise very task specific frontends, which the web browser would open in a new tab or popup window. This would allow the Notebook to provide user interfaces that are not well suited for incorporation into the Notebook UI.

For example, the Notebook could open instances of a text editor, allowing users to edit files without using %load and %%file magic, which works poorly when editing multiple files simultaneously or when working with larger files.

The focus of this proposal is on allowing the Notebook to open new tabs/windows containing frontends which will communicate with the Notebooks own kernel.

These lesser frontends, Notebook Panel Apps, would not normally provide a comprehensive IPython interface. They would depend on the Notebook's UI for that functionality. They are meant to extend the Notebook UI, and would often be useless without it.

# Specific Goal

The goal is to develop a library that includes a generic JavaScript client that could be used by panel app developers for developing secure frontends that the Notebook could instantiate and communicate with.