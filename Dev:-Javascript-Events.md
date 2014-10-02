Javascript events are used to notify unrelated parts of the notebook interface when something happens. For example, if the kernel is busy executing code, it may send an event announcing as such, which can then be picked up by other services, like the notification area.

This page documents the core set of events, and explains when and why they are triggered.

## Kernel events

#### status_started.Kernel

The kernel has been started, but a connection to it has not necessarily been established yet.

* The kernel has been successfully started through `/api/kernels`
* The kernel has been successfully restarted through `/api/kernels`
* The kernel has been successfully started through `/api/sessions`

#### status_connected.Kernel

A connection has been established to the kernel

#### status_restarting.Kernel

The kernel is restarting

#### status_interrupting.Kernel

The kernel is being interrupted

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