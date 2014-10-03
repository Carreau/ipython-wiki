(Note: this page is not currently consistent with IPython master)

Javascript events are used to notify unrelated parts of the notebook interface when something happens. For example, if the kernel is busy executing code, it may send an event announcing as such, which can then be picked up by other services, like the notification area. For details on how the events themselves work, see the [JQuery documentation](http://api.jquery.com/on/).

This page documents the core set of events, and explains when and why they are triggered.

## Kernel-related events

#### kernel_started

The kernel has been started, but a connection to it has not necessarily been established yet.

##### kernel_started.Kernel

* The kernel has been successfully started through `/api/kernels` (triggers "kernel_started.Kernel")
* The kernel has been successfully restarted through `/api/kernels` (triggers "kernel_started.Kernel")

##### kernel_started.Session

* The kernel has been successfully started through `/api/sessions` (triggers "kernel_started.Session")

#### connected.Kernel

A connection has been established to the kernel. This is triggered as soon as all websockets (e.g. to the shell, iopub, and stdin channels) have been opened.

#### status_starting.Kernel

The kernel is starting. This is triggered once when the kernel process is starting up.

#### status_restarting.Kernel

The kernel is restarting. This is triggered at the beginning of an restart call to `/api/kernels`.

##### status_restarting.Kernel.auto

The kernel is restarting on its own. This is only triggered if the `Kernel` receives a status message indicating that it is restarting. This probably also means that something happened to cause the kernel to die.

#### status_interrupting.Kernel

The kernel is being interrupted. This is triggered at the beginning of a interrupt call to `/api/kernels`.

#### status_disconnected.Kernel

The connection to the kernel has been lost.

##### status_disconnected.Kernel.error

Not only was the connection lost, but it was lost due to an error (i.e., we did not tell the websockets to close).

#### status_idle.Kernel

The kernel's execution state is 'idle'.

#### status_busy.Kernel

The kernel's execution state is 'busy'.

#### kernel_dead

The kernel is dead (it may have been alive at some point, or it may not).

##### kernel_dead.Kernel.died

This is triggered if the kernel dies, and the kernel manager attempts to restart it, but is unable to. (TODO: in what scenarios would this actually happen?)

##### kernel_dead.Kernel.killed

The kernel has been manually killed through `/api/kernels`.

##### kernel_dead.Session.error

The kernel could not be started through `/api/sessions`.

##### kernel_dead.Session.killed

The kernel has been manually killed through `/api/sessions`.