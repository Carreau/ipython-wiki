## IPython/Jupyter kernels:

The Kernel Zero, is of course [IPython](https://ipython.org), which you can get though [ipykernel](https://pypi.python.org/pypi/ipykernel), and still comes (for now) as a dependency of [jupyter](https://jupyter.org). The IPython kernel can be thought as a reference implementation, here are other available kernels:

|Number|Name| Jupyter/IPython Version | Language(s) Version | 3rd party dependencies | Example Notebooks | Notes |
|------|----|-------------------------|---------------------|------------------------|-------------------|-------|
|1|[IPyKernel](https://github.com/ipython/ipykernel)|Jupyter 4.0|python 2.7, >= 3.3|pyzmq|||
|2|[IJulia](https://github.com/JuliaLang/IJulia.jl)||julia >= 0.3||||
|3|[IHaskell](https://github.com/gibiansky/IHaskell)||ghc >= 7.6||||
|4|[IRuby](https://github.com/SciRuby/iruby)||ruby >= 2.1||||
|5|[IJavascript](https://github.com/n-riesco/ijavascript)||nodejs >= 0.10||||
|6|[jpCoffeescript](https://github.com/n-riesco/jp-coffeescript)||coffeescript >= 1.7||||
|7|[ICSharp](https://github.com/zabirauf/icsharp)|Jupyter 4.0|C# 4.0+|scriptcs|||
|8|[IRKernel](http://irkernel.github.io/)|IPython 3.0|R 3.2|rzmq|||
|9|[SageMath](http://www.sagemath.org/)|Jupyter 4|Any|many|||
|10|[pari_jupyter](https://github.com/jdemeyer/pari_jupyter)|Jupyter 4|2.8|Cython|||
|11|[IFSharp](https://github.com/fsprojects/IfSharp)|IPython 2.0|F#||[Features](http://nbviewer.ipython.org/github/BayardRock/IfSharp/blob/master/Feature%20Notebook.ipynb)||
|12|[IGo](https://github.com/takluyver/igo)||Go >= 1.4||||
|13|[IScala](https://github.com/mattpap/IScala)||Scala||||
|14|[IErlang](https://github.com/robbielynch/ierlang)|IPython 2.3|Erlang|rebar|||
|15|[ITorch](https://github.com/facebook/iTorch)|IPython >= 2.2|Torch 7 (LuaJIT)||||
|16|[IElixir](https://github.com/pprzetacznik/IElixir)|Jupyter|Elixir||||
|17|[IAldor](https://github.com/mattpap/IAldor)|IPython >= 1|Aldor||||
|18|[IOCaml](https://github.com/andrewray/iocaml)|IPython >= 1.1|OCaml >= 4.01|opam|||
|19|[IForth](https://github.com/jdfreder/iforth)|IPython >= 3|Forth||||
|20|[IPerl](https://metacpan.org/release/Devel-IPerl)||Perl 5||||
|21|[IPerl6](https://github.com/timo/iperl6kernel)||Perl 6||||
|22|[IPHP](https://github.com/dawehner/ipython-php)|IPython >= 2|PHP >= 5.4|composer|||
|23|[IOctave](https://github.com/calysto/octave_kernel)|Jupyter|Octave||[Example](http://nbviewer.jupyter.org/github/Calysto/octave_kernel/blob/master/octave_kernel.ipynb)|MetaKernel|
|24|[IScilab](https://github.com/blink1073/scilab_kernel)|Jupyter|Scilab|scilab2py||Wrapper|
|25|[IMatlab](https://github.com/calysto/matlab_kernel)|Jupyter|Matlab|pymatbridge|[Example](http://nbviewer.ipython.org/github/Calysto/matlab_kernel/blob/master/matlab_kernel.ipynb)|Wrapper|
|26|[Bash](https://github.com/takluyver/bash_kernel)|IPython >= 3|bash|||Wrapper|
|27|[CloJupyter](https://github.com/roryk/clojupyter)|Jupyter|Clojure >= 1.7||||
|28|[Hy Kernel](https://github.com/bollwyvl/hy_kernel/)|Jupyter|Hy||[Tutorial](http://nbviewer.ipython.org/github/bollwyvl/hy_kernel/blob/master/notebooks/Tutorial.ipynb)||
|29|[Redis Kernel](https://github.com/supercoderz/redis_kernel)|IPython >= 3|redis|||Wrapper|
|30|[jove](https://www.npmjs.com/package/jove)||io.js||||
|31|[jp-babel](https://www.npmjs.com/package/jp-babel)|Jupyter|Babel||||
|32|[ICalico](http://calicoproject.org/ICalico)|IPython >= 2|*multiple*||[Index](http://nbviewer.jupyter.org/urls/bitbucket.org/ipre/calico/raw/master/notebooks/Index.ipynb)||
|33|[IMathics](http://nbviewer.ipython.org/gist/sn6uv/8381447)||Mathics||||
|34|[Lua Kernel](https://github.com/neomantra/lua_ipython_kernel)||Lua||||
|35|[IPyLua](https://github.com/pakozm/IPyLua)||Lua|||Fork of *Lua Kernel*|
|36|[Simple example kernel](https://github.com/dsblank/simple_kernel)||||||
|37|[Calysto Scheme](https://github.com/Calysto/calysto_scheme)||Scheme|||MetaKernel|
|38|[Calysto Processing](https://github.com/Calysto/calysto_processing)||Processing.js >= 2|||MetaKernel|
|39|[idl_kernel](https://github.com/lstagner/idl_kernel)||IDL|||IDL seem to have a [built-in kernel](http://www.exelisvis.com/docs/idl_kernel.html) starting with version 8.5|
|40|[Mochi Kernel](https://github.com/pya/mochi-kernel)||Mochi||||
|41|[Lua (used in Splash)](https://github.com/scrapinghub/splash/tree/master/splash/kernel)||Lua||||
|42|[Spark Kernel](https://github.com/apache/incubator-toree)|Jupyter|Scala, Python, R|Spark >= 1.5|||
|43|[Skulpt Python Kernel](https://github.com/Calysto/skulpt_python)||Skulpt Python||[Examples](http://jupyter.cs.brynmawr.edu/hub/dblank/public/Examples/Skulpt%20Python%20Examples.ipynb)|MetaKernel|
|44|[MetaKernel Bash](https://github.com/Calysto/metakernel/tree/master/metakernel_bash)||bash|||MetaKernel|
|45|[MetaKernel Python](https://github.com/Calysto/metakernel/tree/master/metakernel_python)||python|||MetaKernel|
|46|[IVisual](https://pypi.python.org/pypi/IVisual)||VPython||[Ball-in-Box](http://nbviewer.jupyter.org/url/dl.dropboxusercontent.com/u/5095342/visual/Ball-in-Box.ipynb)||
|47|[IBrainfuck](https://github.com/robbielynch/ibrainfuck)||Brainfuck||[Demo](http://nbviewer.jupyter.org/gist/robbielynch/e611442ca2d056f3b78f)|Wrapper|
|48|[KDB+/Q Kernel (IKdbQ)](https://github.com/jvictorchen/IKdbQ)|IPython >= 3.1|Q|qzmq, qcrypt|||
|49|[ICryptol](https://github.com/GaloisInc/ICryptol)||Cryptol|CVC4|||
|50|[C++ (cling)](https://github.com/minrk/clingkernel)||||||
|51|[Xonsh](https://github.com/calysto/xonsh_kernel)||Xonsh||[Example](http://nbviewer.ipython.org/github/Calysto/xonsh_kernel/blob/master/xonsh_kernel.ipynb)|MetaKernel|
|52|[Prolog](https://github.com/Calysto/calysto_prolog)||Prolog|||MetaKernel|
|53|[cl-jupyter](https://github.com/fredokun/cl-jupyter)|Jupyter|Common Lisp|Quicklisp|[About](https://github.com/fredokun/cl-jupyter/blob/master/about-cl-jupyter.ipynb)||
|54|[Maxima-Jupyter](https://github.com/robert-dodier/maxima-jupyter)|Jupyter|Maxima|Quicklisp|||
|55|[Calysto LC3](https://github.com/Calysto/calysto_lc3)|||||Assembly Language for the [Little Computer 3](https://en.wikipedia.org/wiki/LC-3)|
|56|[Java 9 Kernel](https://github.com/Bachmann1234/java9_kernel)||Java >= 9||||
|57|[Yacas](https://github.com/grzegorzmazur/yacas_kernel)||YACAS||||
|58|[IJython](https://github.com/suvarchal/IJython)||Jython 2.5||||
|59|[ROOT](https://github.com/root-mirror/root/tree/master/bindings/pyroot/JupyROOT)|Jupyter|C++/python|ROOT >= 6.05|||
|60|[Gnuplot Kernel](https://github.com/has2k1/gnuplot_kernel)||Gnuplot||[Example](https://github.com/has2k1/gnuplot_kernel/tree/master/examples)|MetaKernel|

Many kernels are available for installation on [PyPI](https://pypi.python.org/pypi?:action=browse&c=586).

[Brython Magics](https://github.com/kikocorreoso/brythonmagic)

### Repository

Several of these kernels are available in a PPA for Ubuntu 15.10 (wily)/16.04 (xenial). Add it with:

    sudo add-apt-repository ppa:chronitis/jupyter
    sudo apt update
    sudo apt install <kernelname>

The following kernel packages are available (along with their dependencies):

 * `ihaskell` (errata as of 2016-01-16: `hlint` integration doesn't work, disable with `:opt no-hlint`)
 * `ijulia`
 * `ijavascript`
 * `jp-coffeescript`
 * `irkernel`
 * `iruby`

(The repository doesn't contain ipython/jupyter - you'll still need to install it with `pip`)

## Additional Related Projects

*   [Jove](https://github.com/jove-sh) - notebook interface in Java; provides Spark and Scala kernels

## Creating new Jupyter kernels

[Making kernels for Jupyter](http://jupyter-client.readthedocs.org/en/latest/kernels.html) in the documentation.

[IHaskell creator blog
post](http://andrew.gibiansky.com/blog/ipython/ipython-kernels/)

[Testing kernels against message specification (work in progress)](https://github.com/ipython/ipython/wiki/Dev:-Testing-kernels-against-message-specification)

[Tool to test a kernel against specification (work in progress)](https://github.com/jupyter/jupyter_kernel_test)
