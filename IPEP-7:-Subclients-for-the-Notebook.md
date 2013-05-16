**NOTE** This IPEP is stale and has been replaced by [IPEP18: Text Editor for the Notebook](https://github.com/ipython/ipython/wiki/IPEP18:-Text-Editor-for-the-Notebook), along with another IPEP regarding a HTML terminal, which will be written up shortly. This IPEP is no longer being discussed.

This IPEP advances the idea of subclients.

The goal is to allow the Notebook to spawn user interfaces in separate windows, providing interfaces that are not well suited for incorporation into the Notebook UI.

For example, the Notebook might have a magic, say %edit, that takes a path to a file, then spawns an instance of a text editor, allowing users to edit the file in a new tab.