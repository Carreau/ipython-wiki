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

#### status_restarting.Kernel

The kernel is restarting. This is triggered at the beginning of an restart call to `/api/kernels`.

##### status_restarting.Kernel.auto

The kernel is restarting on its own. This is only triggered if the `Kernel` receives a status message indicating that it is restarting. 

#### status_interrupting.Kernel

The kernel is being interrupted. This is triggered at the beginning of a interrupt call to `/api/kernels`.

#### status_disconnected.Kernel

There is no connection to the kernel (either it has been lost, or there never was one)

#### early_disconnect.Kernel

A connection could not be made to the kernel

#### status_dead.Kernel

The kernel was alive at some point, but has since died

#### no_kernel.Kernel

There is no kernel (either it could never be started, or it died)

#### status_idle.Kernel

The kernel is idle

#### status_busy.Kernel

The kernel is processing a message