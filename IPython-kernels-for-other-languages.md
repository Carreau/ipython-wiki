## IPython/Jupyter kernels:

The Kernel Zero, is of course [IPython](https://ipython.org), which you can get through [ipykernel](https://pypi.python.org/pypi/ipykernel), and still comes (for now) as a dependency of [jupyter](https://jupyter.org). The IPython kernel can be thought as a reference implementation, here are other available kernels:

|Name| Jupyter/IPython Version | Language(s) Version | 3rd party dependencies | Example Notebooks | Notes |
|----|-------------------------|---------------------|------------------------|-------------------|-------|
|[IPyKernel](https://github.com/ipython/ipykernel)|Jupyter 4.0|python 2.7, >= 3.3|pyzmq|||
|[IJulia](https://github.com/JuliaLang/IJulia.jl)||julia >= 0.3||||
|[IHaskell](https://github.com/gibiansky/IHaskell)||ghc >= 7.6||||
|[IRuby](https://github.com/SciRuby/iruby)||ruby >= 2.1||||
|[IJavascript](https://github.com/n-riesco/ijavascript)||nodejs >= 0.10||||
|[jpCoffeescript](https://github.com/n-riesco/jp-coffeescript)||coffeescript >= 1.7||||
|[ICSharp](https://github.com/zabirauf/icsharp)|Jupyter 4.0|C# 4.0+|scriptcs|||
|[IRKernel](http://irkernel.github.io/)|IPython 3.0|R 3.2|rzmq|||
|[SageMath](http://www.sagemath.org/)|Jupyter 4|Any|many|||
|[pari_jupyter](https://github.com/jdemeyer/pari_jupyter)|Jupyter 4|2.8|Cython|||
|[IFSharp](https://github.com/fsprojects/IfSharp)|IPython 2.0|F#||[Features](http://nbviewer.ipython.org/github/BayardRock/IfSharp/blob/master/Feature%20Notebook.ipynb)||
|[Gophernotes](https://github.com/gophergala2016/gophernotes)|Jupyter 4|Go >= 1.4|zeromq 2.2.x|[examples](https://github.com/gophergala2016/gophernotes/tree/master/examples)|[docker image](https://hub.docker.com/r/dwhitena/gophernotes/)|
|[IGo](https://github.com/takluyver/igo)||Go >= 1.4||||
|[IScala](https://github.com/mattpap/IScala)||Scala||||
|[Jupyter-scala](https://github.com/alexarchambault/jupyter-scala)|IPython>=3.0|Scala>=2.10||[example](https://github.com/alexarchambault/jupyter-scala/blob/master/examples/tutorials/Macrology.ipynb)||
|[IErlang](https://github.com/robbielynch/ierlang)|IPython 2.3|Erlang|rebar|||
|[ITorch](https://github.com/facebook/iTorch)|IPython >= 2.2|Torch 7 (LuaJIT)||||
|[IElixir](https://github.com/pprzetacznik/IElixir)|Jupyter|Elixir||||
|[IAldor](https://github.com/mattpap/IAldor)|IPython >= 1|Aldor||||
|[IOCaml](https://github.com/andrewray/iocaml)|IPython >= 1.1|OCaml >= 4.01|opam|||
|[IForth](https://github.com/jdfreder/iforth)|IPython >= 3|Forth||||
|[IPerl](https://metacpan.org/release/Devel-IPerl)||Perl 5||||
|[IPerl6](https://github.com/timo/iperl6kernel)||Perl 6||||
|[IPHP](https://github.com/dawehner/ipython-php)|IPython >= 2|PHP >= 5.4|composer|||
|[IOctave](https://github.com/calysto/octave_kernel)|Jupyter|Octave||[Example](http://nbviewer.jupyter.org/github/Calysto/octave_kernel/blob/master/octave_kernel.ipynb)|MetaKernel|
|[IScilab](https://github.com/calysto/scilab_kernel)|Jupyter|Scilab||[Example](http://nbviewer.jupyter.org/github/Calysto/scilab_kernel/blob/master/scilab_kernel.ipynb)|MetaKernel|
|[IMatlab](https://github.com/calysto/matlab_kernel)|Jupyter|Matlab|pymatbridge|[Example](http://nbviewer.ipython.org/github/Calysto/matlab_kernel/blob/master/matlab_kernel.ipynb)|MetaKernel|
|[Bash](https://github.com/takluyver/bash_kernel)|IPython >= 3|bash|||Wrapper|
|[PowerShell](https://github.com/vors/jupyter-powershell)|IPython >= 3|Windows|||Wrapper, Based on Bash Kernel|
|[CloJupyter](https://github.com/roryk/clojupyter)|Jupyter|Clojure >= 1.7||||
|[Hy Kernel](https://github.com/bollwyvl/hy_kernel/)|Jupyter|Hy||[Tutorial](http://nbviewer.ipython.org/github/bollwyvl/hy_kernel/blob/master/notebooks/Tutorial.ipynb)||
|[Redis Kernel](https://github.com/supercoderz/redis_kernel)|IPython >= 3|redis|||Wrapper|
|[jove](https://www.npmjs.com/package/jove)||io.js||||
|[jp-babel](https://www.npmjs.com/package/jp-babel)|Jupyter|Babel||||
|[ICalico](http://calicoproject.org/ICalico)|IPython >= 2|*multiple*||[Index](http://nbviewer.jupyter.org/urls/bitbucket.org/ipre/calico/raw/master/notebooks/Index.ipynb)||
|[IMathics](http://nbviewer.ipython.org/gist/sn6uv/8381447)||Mathics||||
|[Lua Kernel](https://github.com/neomantra/lua_ipython_kernel)||Lua||||
|[IPyLua](https://github.com/pakozm/IPyLua)||Lua|||Fork of *Lua Kernel*|
|[Calysto Scheme](https://github.com/Calysto/calysto_scheme)||Scheme|||MetaKernel|
|[Calysto Processing](https://github.com/Calysto/calysto_processing)||Processing.js >= 2|||MetaKernel|
|[idl_kernel](https://github.com/lstagner/idl_kernel)||IDL|||IDL seem to have a [built-in kernel](http://www.exelisvis.com/docs/idl_kernel.html) starting with version 8.5|
|[Mochi Kernel](https://github.com/pya/mochi-kernel)||Mochi||||
|[Lua (used in Splash)](https://github.com/scrapinghub/splash/tree/master/splash/kernel)||Lua||||
|[Apache Toree (formerly Spark Kernel)](https://github.com/apache/incubator-toree)|Jupyter|Scala, Python, R|Spark >= 1.5|||
|[Skulpt Python Kernel](https://github.com/Calysto/skulpt_python)||Skulpt Python||[Examples](http://jupyter.cs.brynmawr.edu/hub/dblank/public/Examples/Skulpt%20Python%20Examples.ipynb)|MetaKernel|
|[MetaKernel Bash](https://github.com/Calysto/metakernel/tree/master/metakernel_bash)||bash|||MetaKernel|
|[MetaKernel Python](https://github.com/Calysto/metakernel/tree/master/metakernel_python)||python|||MetaKernel|
|[IVisual](https://pypi.python.org/pypi/IVisual)||VPython||[Ball-in-Box](http://nbviewer.jupyter.org/url/dl.dropboxusercontent.com/u/5095342/visual/Ball-in-Box.ipynb)||
|[IBrainfuck](https://github.com/robbielynch/ibrainfuck)||Brainfuck||[Demo](http://nbviewer.jupyter.org/gist/robbielynch/e611442ca2d056f3b78f)|Wrapper|
|[KDB+/Q Kernel (IKdbQ)](https://github.com/jvictorchen/IKdbQ)|IPython >= 3.1|Q|qzmq, qcrypt|||
|[ICryptol](https://github.com/GaloisInc/ICryptol)||Cryptol|CVC4|||
|[C++ (cling)](https://github.com/minrk/clingkernel)||||||
|[Xonsh](https://github.com/calysto/xonsh_kernel)||Xonsh||[Example](http://nbviewer.ipython.org/github/Calysto/xonsh_kernel/blob/master/xonsh_kernel.ipynb)|MetaKernel|
|[Prolog](https://github.com/Calysto/calysto_prolog)||Prolog|||MetaKernel|
|[cl-jupyter](https://github.com/fredokun/cl-jupyter)|Jupyter|Common Lisp|Quicklisp|[About](https://github.com/fredokun/cl-jupyter/blob/master/about-cl-jupyter.ipynb)||
|[Maxima-Jupyter](https://github.com/robert-dodier/maxima-jupyter)|Jupyter|Maxima|Quicklisp|||
|[Calysto LC3](https://github.com/Calysto/calysto_lc3)|||||Assembly Language for the [Little Computer 3](https://en.wikipedia.org/wiki/LC-3)|
|[Java 9 Kernel](https://github.com/Bachmann1234/java9_kernel)||Java >= 9||||
|[Yacas](https://github.com/grzegorzmazur/yacas_kernel)||YACAS||||
|[IJython](https://github.com/suvarchal/IJython)||Jython 2.5||||
|[ROOT](https://github.com/root-mirror/root/tree/master/bindings/pyroot/JupyROOT)|Jupyter|C++/python|ROOT >= 6.05|||
|[Gnuplot Kernel](https://github.com/has2k1/gnuplot_kernel)||Gnuplot||[Example](https://github.com/has2k1/gnuplot_kernel/tree/master/examples)|MetaKernel|
|[Tcl](https://github.com/ryanpepper/tcl_kernel)|Jupyter|Tcl 8.5|||Based on Bash Kernel|
|[J](https://github.com/martin-saurer/jkernel)|Jupyter|J 804||||

Many kernels are available for installation on [PyPI](https://pypi.python.org/pypi?:action=browse&c=586).

### Repository

Several of these kernels are available in a PPA for Ubuntu 15.10 (wily)/16.04 (xenial). Add it with:

    sudo add-apt-repository ppa:chronitis/jupyter
    sudo apt update
    sudo apt install <kernelname>

The following kernel packages are available (along with their dependencies):

 * `ihaskell` (errata as of 2016-01-16: `hlint` integration doesn't work, disable with `:opt no-lint`)
 * `ijulia`
 * `ijavascript`
 * `jp-coffeescript`
 * `irkernel`
 * `iruby`
 * `gophernotes` (xenial only)

(The repository doesn't contain ipython/jupyter - you'll still need to install it with `pip`)

## Additional Related Projects

*   [Jove](https://github.com/jove-sh) - notebook interface in Java; provides Spark and Scala kernels
*   [Brython Magics](https://github.com/kikocorreoso/brythonmagic) - A magic trick to allow you to use Brython code (client-side) in other notebooks

## Creating new Jupyter kernels

[Making kernels for Jupyter](http://jupyter-client.readthedocs.org/en/latest/kernels.html) in the documentation.

[Simple example kernel](https://github.com/dsblank/simple_kernel)

[IHaskell creator blog
post](http://andrew.gibiansky.com/blog/ipython/ipython-kernels/)

[Testing kernels against message specification (work in progress)](https://github.com/ipython/ipython/wiki/Dev:-Testing-kernels-against-message-specification)

[Tool to test a kernel against specification (work in progress)](https://github.com/jupyter/jupyter_kernel_test)
