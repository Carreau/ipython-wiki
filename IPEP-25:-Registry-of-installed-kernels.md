<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Thomas Kluyver &lt;thomas@kluyver.me.uk&gt;</td></tr>
<tr><td> Created </td><td> April 8, 2014</td></tr>
<tr><td> Updated </td><td> April 9, 2014</td></tr>
<tr><td> Discussion </td><td> <a href="https://github.com/ipython/ipython/issues/5656">Issue 5656</a> </td></tr>
<tr><td> Implementation </td><td> <a href="https://github.com/ipython/ipython/pull/5598">PR 5598</a>, 
<a href="https://github.com/ipython/ipython/pull/5796">PR 5796</a>,
<a href="https://github.com/ipython/ipython/pull/5827">PR 5827</a>
</td></tr>
</table>

#Abstract

IPython's messaging protocol, originally designed to allow multiple frontends, has proven invaluable for supporting kernels in different languages, such as [Julia](https://github.com/JuliaLang/IJulia.jl), [Haskell](https://github.com/gibiansky/IHaskell) and [R](https://github.com/takluyver/IRkernel/), and we are rapidly adapting the protocol to be more language agnostic. However, using an alternative kernel requires a long, awkward command line argument, and you must start one notebook server for each kernel you wish to use.

This IPEP describes a way for available kernels to advertise themselves to IPython through a registry, and the changes to IPython's API to allow querying the registry and launching kernels. It does not cover the UI which will allow users to start different kernels.

#Registry locations

The registry lives in two directories on the filesystem: a per-user directory and a systemwide directory. If both contain a kernel with the same name, the user directory has priority.

The per-user directory is located in `$IPYTHONDIR/kernels/` (typically `~/.ipython/kernels/`)

The systemwide directory is located in:
* Unix systems: `/usr/share/ipython/kernels`
* Windows systems: `%PROGRAMDATA%\ipython\kernels` (this is not defined on XP)

#Kernel directories

Each kernel in the registry is represented by a directory in one of the locations described above. The name of this directory is the kernel's name, and is used in the API to list and select the kernel. Kernel names should consist solely of ASCII alphanumeric characters and underscores, and should not start with a number (i.e. they should match the regex `[a-zA-Z_][a-zA-Z0-9_]*`). Names are case insensitive.

A number of files should be present in the directory:

* kernel.json: contains important information about the kernel; described below.
* icon.png, icon-small.png: Icons to display in the user interface for the kernel. TBD: sizes
* custom.js, custom.css (optional): JS and CSS to tweak the interactive notebook UI. These are provided so kernels can work around limitations in the UI, but they should be used no more than necessary, and be treated as a stopgap measure while the core UI is improved to better accommodate different languages.

##kernel.json

Should contain an object with the following keys:

* argv: A list of command line arguments used to start the kernel. The text `{connection_file}` in any argument will be replaced with the path to the connection file.
* display_name: The kernel's name as it should be displayed in the UI. Unlike the kernel name used in the API, this can contain arbitrary unicode characters.
* language: The programming language which this kernel runs. This will be stored in notebook metadata.
* codemirror_mode (optional): The codemirror mode to use for code in this language. This can be a string or a dictionary, as passed to [codemirror config](http://codemirror.net/doc/manual.html#config). The string from `language` will be used if this is not provided.
* env (optional): A dictionary of environment variables to set for the kernel. These will be added to the current environment variables.
* help_links (optional): A list of dictionaries, each with keys 'text' and 'url'. These will be displayed in the help menu in the notebook UI.
* (TBD: mimetype? pygments_lexer?)

#New APIs

The APIs will be in `IPython.kernel`. A new module will provide a `KernelSpec` class, instances of which have attributes matching the keys in kernel.json, along with a `kernel_directory` attribute providing the path to the kernel directory. This module will also have functions to list available kernels and retrieve a kernel by name.

KernelManager will gain a `kernel_spec` attribute, referring to `KernelSpec` instances, and be refactored to use this to launch kernels. It will not be configurable, because a single process may well instantiate multiple `KernelManager`s with different kernels. The configurable `kernel_cmd` will remain for backwards compatibility, and override `kernel_spec` when set, although this will issue a warning.

TBD: Caching?

#The Native Kernel

The kernel named `python2` or `python3`, corresponding to the Python version in the parent process, will be treated specially. It will always be available, even if its kernel directory is removed. Its `argv` will be overridden to use the same Python executable as the parent process; this means that a notebook server started in a virtualenv will start its native kernels in that virtualenv.

The kernel name `python` will be special cased as an alias to the native kernel.

# REST API

Three new REST endpoints will be defined to access this information.

## List available kernels

    GET /api/kernelspecs

### Response

    status: 200 OK

```json
[
    {
        "name": "python3",
        "display_name": "Python 3"
    },
    {
        "name": "julia",
        "display_name": "Julia"
    }
]
```

## Get information for a kernel

    GET /api/kernelspecs/[:kernel]

### Response

    status: 200 OK

The response body is the contents of `kernel.json`.

If the kernel is not found, the status will be 404.

## Get a file from a kernel directory

    GET /kernelspecs/[:kernel]/[:filename]

The responses follow standard semantics for serving files over HTTP.