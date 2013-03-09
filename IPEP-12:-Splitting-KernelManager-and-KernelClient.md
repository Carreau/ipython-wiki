The KernelManager is really in charge of two separate tasks,
and should be two separate classes.
First, the KernelManager is the object in charge of creating and monitoring
a Kernel subprocess.
Second, the KernelManager is the object that mediates *communicating* with a Kernel via Channels.
We will call this second object a KernelClient.
The fact that these two tasks exist in one object has been problematic.

- For any given Kernel, there is always exactly one KernelManager,
  but there can be zero-to-many KernelClients (e.g. `ipython qtconsole --existing`).
- The way a process communicates with a Kernel may vary,
  without varying the way the Kernel is monitored (see Notebook, which doesn't use channels).
- There is no clear mechanism for remote signaling / restarting from connected clients.


## Proposal: split KernelManager and KernelClient

The main proposal is to split the current KernelManager into two classes:

- `KernelManager` will only contain process management / monitoring capabilities
- `KernelClient` will only contain kernel *communication* capabilities (channels)

And add a `control` channel from IPython.parallel, which will be described below.

There are a couple of hairy bits where the channels and process management are entangled.
Detangling them will require some changing / cleanup of logic.


### Shutdown

Currently, a clean shutdown of a kernel involves sending a message via the shell channel.
A Control channel will be added for this kind of message.
The Kernel already uses this Control channel in IPython.parallel
for queue priority reasons (abort / shutdown must not queue behind execute requests).
The KernelManager would connect to the Control channel to send shutdown / restart / abort messages.


### Heartbeat

Heartbeat logic is also unclean, because the heartbeat channel is used to detect kernel death.
But this mechanism is only used because we cannot make the assumption that the Kernel is local.
By splitting the KernelClient and the KernelManager,
the location of the kernel is no longer ambiguous.
Cleaning up the hearbeat logic has already [begun](https://github.com/ipython/ipython/pull/2873),
but I will document the changes here, along with the more complete plan.

- The KernelManager is able to directly determine if the Kernel is alive,
  because it owns the process.
- Heartbeat channel would be used by Clients for *monitoring purposes only*.
  Clients are never responsible for restarting the kernel when the heart stops.
  This is important for cases like the notebook, where Clients are not necessarily running when the heart stops.
- KernelManager will always automatically restart the Kernel when it dies.


### Never a Kernel without a KernelManager

A Kernel should never exist without a KernelManager.
Right now, this can happen when you type `ipython kernel`,
which results in an uninterruptible process that is super annoying.

The `ipython kernel` entrypoint should directly start a *KernelManager*,
which in turn starts the actual kernel.
This will allow things like ^C to halt the Kernel,
without having to send shutdown messages from Clients.

### Module layout

With the kernel modules no longer in `IPython.zmq` next to disparate,
their current `IPython.kernel.kernelmanager` names are super annoying and redundant.
The layout will be changes, to reflect the new organization and reduced ambiguity:

```
IPython/kernel/manager.py \
IPython/kernel/client.py   | - these three files will contain what used to be in kernelmanager.py
IPython/kernel/channels.py /
IPython/blocking/client.py - what used to be blockingkernelmanager.py
```

etc.

### Base channels shouldn't be threads

I haven't quite decided what this should look like,
but the base channel classes should *not* be Thread subclasses.
This adds far too much unnecessary complexity,
and is totally inappropriate for cases like the BlockingKernelClient.
It may be that the Qt case will still want threads,
but this should be handled there, rather than in the base class.

## Stage two: remote signal / restart

What might not be a part of the initial work,
but is facilitated by the new design,
is the ability for Clients to request restarts of the Kernel.
All of this will be done via the Control channel,
with which Clients connect to the KernelManager,
which then performs operations on the Kernel process.
This will allow remote Clients to request actions,
such as signal / interrupt / restart / shutdown,
which are actually executed by the KernelManager.
This will be more fully described in a future IPEP / PR.


## Stage two and a half: remote signal / restart *engines*

A long-term goal is the complete consolidation of `ipengine` and `ipython kernel`.
Ultimately, the only difference between the two should be the connection direction.
With the KernelManager / Control channel architecture fully implemented,
Engines should each have a KernelManager,
which will allow the remote signal / restart functionality of stage two to become available
in IPython.parallel as well, which is sorely needed.