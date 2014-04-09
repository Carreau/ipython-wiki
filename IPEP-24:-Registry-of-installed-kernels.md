<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Thomas Kluyver &lt;thomas@kluyver.me.uk&gt;</td></tr>
<tr><td> Created </td><td> April 8, 2014</td></tr>
<tr><td> Updated </td><td> April 8, 2014</td></tr>
<tr><td> Discussion </td><td>  </td></tr>
<tr><td> Implementation </td><td>  </td></tr>
</table>

#Abstract

IPython's messaging protocol, originally designed to allow multiple frontends, has proven invaluable for supporting kernels in different languages, such as [Julia](https://github.com/JuliaLang/IJulia.jl), [Haskell](https://github.com/gibiansky/IHaskell) and [R](https://github.com/takluyver/IRkernel/), and we are rapidly adapting the protocol to be more language agnostic. However, using an alternative kernel requires a long, awkward command line argument, and you must start one notebook server for each kernel you wish to use.

This IPEP describes a way for available kernels to advertise themselves to IPython through a registry, and the changes to IPython's API to allow querying the registry and launching kernels. It does not cover the UI which will allow users to start different kernels.