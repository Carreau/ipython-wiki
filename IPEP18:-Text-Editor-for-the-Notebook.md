The Notebook interface was never designed to function as a text editor. That said, Notebook users do need to edit text files, and will often try to subvert the interface to do so.

The proposed solution is to allow the Notebook to open a text editor in a new tab.

## A Rough Sketch

The user would have a magic, say `edit`, which would take a path to the file they wanted to hack on, so running `%edit script.py` would open the editor in a new tab, with `script.py` open.

Note that each time `edit` is called, a new instance of the editor will be created in a new browser tab. Each editor instance is created to edit a single file.

Each editor instance will display the current file name in the browser tab using the `<title>` element, and display the state of the editor (think saved/unsaved changes) by updating the favicon. This allows the browser's native tabs to be used to manage multiple instances of the editor or Notebook.

The editor will support Save and Save-As operations, but not Open or New, which will be features of the Notebook's 'edit' magic. Not sure about Run??

## Issues

Actually implementing an editor should be pretty straight forwards, so why hasn't it been done?

- While ACE and CodeMirror seem to be the only real choices regarding which editor to use, we still need to pick one of them.
- The Web API still needs specifying.
- The extension to the Notebook interface needs to be discussed. For starters, should `%%edit` open the cell in the editor? Should the magic be called `edit` at all?