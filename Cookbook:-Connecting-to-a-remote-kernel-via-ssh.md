This recipe explains how to set up a kernel on a remote machine (server), and interact with the kernel on a local machine (client) via a qtconsole. This is done by setting up a series of ssh tunnels between the machines to securely carry messages between the console and kernel. 

## When server is visible from the client

Start a kernel on machine `server`, using either `ipython kernel` or `ipython qtconsole`:

    [server] $ jupyter --runtime-dir
    /run/user/1000/jupyter
    [server] $ ipython kernel
    [IPKernelApp] To connect another client to this kernel, use:
    [IPKernelApp] --existing kernel-1234.json

Which is now listening on localhost by default. 

Now we need to get that connection file to `client`:

    [client] $ scp server:/run/user/1000/jupyter/kernel-1234.json ./

(*NOTE:* In previous versions it may be in `/Users/you/.ipython/profile_default/security/kernel-1234.json`.)

and the next time we start a frontend (`ipython console`, `qtconsole`, etc.), just add `--ssh server`:

    [client] $ ipython qtconsole --existing ./kernel-1234.json --ssh server

which will set up SSH tunnels, and open the right connections.


### Setting up manual SSH tunnels

If the automatic tunneling doesn't work for some reason, you can set up your own tunnels:

    [client] $ cat kernel-1234.json

    {
      "stdin_port": 62719, 
      "ip": "127.0.0.1", 
      "control_port": 58473, 
      "hb_port": 59591, 
      "signature_scheme": "hmac-sha256", 
      "key": "6a282934-9cc9-4e9c-9f07-2acf1b4964cb", 
      "shell_port": 55763, 
      "transport": "tcp", 
      "iopub_port": 49958
    }
    [client] $ for port in $(cat kernel-1234.json | grep '_port' | grep -o '[0-9]\+'); do
    > ssh kerbin -f -N -L $port:127.0.0.1:$port
    > done

(the `-f -N` just mean "run in the background, and don't do anything but tunnel")

That is to say, if *on client* you now try to connect to localhost:55460, the connection will be forwarded to localhost:55460 *on server*, etc.

At this point, you can skip the `--ssh server` piece:

    client > ipython qtconsole --existing ./kernel-1234.json

and you will have a qtconsole that is using the same kernel as the one on the server.

## When server is not visible from the client

The one extra step is that you might want to connect to a machine that is not visible from client.  Let's say there is now a machine `login` that has direct access to server, and login is visible on the internet, but server is not.

The change on server is to listen on an external IP, either 0.0.0.0 or a specific interface:

    server > ipython qtconsole --ip=192.168.1.2
    [IPKernelApp] To connect another client to this kernel, use:
    [IPKernelApp] --existing kernel-4321.json

Now you need to tunnel slightly differently, because you want local ports on client to point to `server` via `login`, rather than `127.0.0.1` via `server`.

Use the same information to get the connection info, but this time use `--ssh login`.

    [client] $ scp server:/Users/you/.ipython/profile_default/security/kernel-4321.json ./
    [client] $ ipython qtconsole --existing ./kernel-4321.json --ssh login

Again, you can set up manual tunnels, but they will be slightly different:

    [client] $ ssh login -f -N -L 55460:server:55460
    etc.

That is, when client asks for these ports on localhost, they will actually get the ports on server, using login as an intermediary.


## `%connect_info`

If copying files is inconvenient, you can use the `%connect_info` magic in an already connected client (e.g. the notebook) to get the contents of the connection file.  You can then write this to a local JSON file manually, and proceed as a bove.