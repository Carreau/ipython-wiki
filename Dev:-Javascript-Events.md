(Note: this page is not currently consistent with IPython master)

Javascript events are used to notify unrelated parts of the notebook interface when something happens. For example, if the kernel is busy executing code, it may send an event announcing as such, which can then be picked up by other services, like the notification area. For details on how the events themselves work, see the [JQuery documentation](http://api.jquery.com/on/).

This page documents the core set of events, and explains when and why they are triggered.

## Cell-related events

* [command_mode.Cell](#command_modecell)
* [create.Cell](#createcell)
* [delete.Cell](#deletecell)
* [edit_mode.Cell](#edit_modecell)
* [select.Cell](#selectcell)
* [output_appended.OutputArea](#output_appendedoutputarea)

## CellToolbar-related events

* [preset_activated.CellToolbar](#preset_activatedcelltoolbar)
* [preset_added.CellToolbar](#preset_addedcelltoolbar)

## Dashboard-related events

* [app_initialized.DashboardApp](#app_initializeddashboardapp)
* [sessions_loaded.Dashboard](#sessions_loadeddashboard)

## Kernel-related events

* [execution_request.Kernel](#execution_requestkernel)
* [input_reply.Kernel](#input_replykernel)
* [kernel_autorestarting.Kernel](#kernel_autorestartingkernel)
* [kernel_busy.Kernel](#kernel_busykernel)
* [kernel_connected.Kernel](#kernel_connectedkernel)
* [kernel_connection_failed.Kernel](#kernel_connection_failedkernel)
* [kernel_created.Kernel](#kernel_createdkernel)
* [kernel_created.Session](#kernel_createdsession)
* [kernel_dead.Kernel](#kernel_deadkernel)
* [kernel_dead.Session](#kernel_deadsession)
* [kernel_disconnected.Kernel](#kernel_disconnectedkernel)
* [kernel_idle.Kernel](#kernel_idlekernel)
* [kernel_interrupting.Kernel](#kernel_interruptingkernel)
* [kernel_killed.Kernel](#kernel_killedkernel)
* [kernel_killed.Session](#kernel_killedsession)
* [kernel_ready.Kernel](#kernel_readykernel)
* [kernel_reconnecting.Kernel](#kernel_reconnectingkernel)
* [kernel_restarting.Kernel](#kernel_restartingkernel)
* [kernel_starting.Kernel](#kernel_startingkernel)
* [send_input_reply.Kernel](#send_input_replykernel)
* [shell_reply.Kernel](#shell_replykernel)
* [spec_changed.Kernel](#spec_changedkernel)

#### kernel_created.Kernel

The kernel has been successfully created or re-created through `/api/kernels`, but a connection to it has not necessarily been established yet.

#### kernel_created.Session

The kernel has been successfully created or re-created through `/api/sessions`, but a connection to it has not necessarily been established yet.

#### kernel_reconnecting.Kernel

An attempt is being made to reconnect (via websockets) to the kernel after having been disconnected.

#### kernel_connected.Kernel

A connection has been established to the kernel. This is triggered as soon as all websockets (e.g. to the shell, iopub, and stdin channels) have been opened. This does not necessarily mean that the kernel is ready to do anything yet, though.

#### kernel_starting.Kernel

The kernel is starting. This is triggered once when the kernel process is starting up, and can be sent as a message by the kernel, or may be triggered by the frontend if it knows the kernel is starting (e.g., it created the kernel and is connected to it, but hasn't been able to communicate with it yet).

#### kernel_ready.Kernel

Like kernel_idle.Kernel, but triggered after the kernel has fully started up.

#### kernel_restarting.Kernel

The kernel is restarting. This is triggered at the beginning of an restart call to `/api/kernels`.

#### kernel_autorestarting.Kernel

The kernel is restarting on its own, which probably also means that something happened to cause the kernel to die. For example, running the following code in the notebook would cause the kernel to autorestart:

```
import os
os._exit(1)
```

#### kernel_interrupting.Kernel

The kernel is being interrupted. This is triggered at the beginning of a interrupt call to `/api/kernels`.

#### kernel_disconnected.Kernel

The connection to the kernel has been lost.

#### kernel_connection_failed.Kernel

Not only was the connection lost, but it was lost due to an error (i.e., we did not tell the websockets to close).

#### kernel_idle.Kernel

The kernel's execution state is 'idle'.

#### kernel_busy.Kernel

The kernel's execution state is 'busy'.

#### kernel_killed.Kernel

The kernel has been manually killed through `/api/kernels`.

#### kernel_killed.Session

The kernel has been manually killed through `/api/sessions`.

#### kernel_dead.Kernel

This is triggered if the kernel dies, and the kernel manager attempts to restart it, but is unable to. For example, the following code run in the notebook will cause the kernel to die and for the kernel manager to be unable to restart it:

```
import os
from IPython.kernel.connect import get_connection_file
with open(get_connection_file(), 'w') as f:
    f.write("garbage")
os._exit(1)
```

#### kernel_dead.Session

The kernel could not be started through `/api/sessions`. This might be because the requested kernel type isn't installed. Another reason for this message is that the kernel died or was killed, but the session wasn't.

## Notebook-related events

* [app_initialized.NotebookApp](#app_initializednotebookapp)
* [autosave_disabled.Notebook](#autosave_disablednotebook)
* [autosave_enabled.Notebook](#autosave_enablednotebook)
* [checkpoint_created.Notebook](#checkpoint_creatednotebook)
* [checkpoint_delete_failed.Notebook](#checkpoint_delete_failednotebook)
* [checkpoint_deleted.Notebook](#checkpoint_deletednotebook)
* [checkpoint_failed.Notebook](#checkpoint_failednotebook)
* [checkpoint_restore_failed.Notebook](#checkpoint_restore_failednotebook)
* [checkpoint_restored.Notebook](#checkpoint_restorednotebook)
* [checkpoints_listed.Notebook](#checkpoints_listednotebook)
* [command_mode.Notebook](#command_modenotebook)
* [edit_mode.Notebook](#edit_modenotebook)
* [list_checkpoints_failed.Notebook](#list_checkpoints_failednotebook)
* [notebook_load_failed.Notebook](#notebook_load_failednotebook)
* [notebook_loaded.Notebook](#notebook_loadednotebook)
* [notebook_loading.Notebook](#notebook_loadingnotebook)
* [notebook_rename_failed.Notebook](#notebook_rename_failednotebook)
* [notebook_renamed.Notebook](#notebook_renamednotebook)
* [notebook_restoring.Notebook](#notebook_restoringnotebook)
* [notebook_save_failed.Notebook](#notebook_save_failednotebook)
* [notebook_saved.Notebook](#notebook_savednotebook)
* [notebook_saving.Notebook](#notebook_savingnotebook)
* [rename_notebook.Notebook](#rename_notebooknotebook)
* [selected_cell_type_changed.Notebook](#selected_cell_type_changednotebook)
* [set_dirty.Notebook](#set_dirtynotebook)
* [set_next_input.Notebook](#set_next_inputnotebook)
* [trust_changed.Notebook](#trust_changednotebook)

## Other

* [open_with_text.Pager](#open_with_textpager)
* [rebuild.QuickHelp](#rebuildquickhelp)