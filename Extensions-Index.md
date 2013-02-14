Extensions you can install for IPython are listed here. To create a new extension, please refer to the [extension docs](http://ipython.org/ipython-doc/dev/config/extensions/index.html).

## physics

Enables calculations involving units, such as `(5 m/s) * (3 s)`. It also defines a range of physical constants, such as the speed of light in a vacuum, and the mass of an electron.

[Homepage](https://bitbucket.org/birkenfeld/ipython-physics)

To install:

```text
    %install_ext https://bitbucket.org/birkenfeld/ipython-physics/raw/default/physics.py
```

Note: In the latest version, quantities with uncertainties are supported if the 
`uncertainties` module is installed.

## `%hierarchy` and `%%dot` magics

The `%hierarchy` magic command draws an inheritance diagram of the given class or object.
With the `%%dot` cell magic, you can write graphiz dot language in a cell.

[Homepage](https://github.com/tkf/ipython-hierarchymagic)

To install:

```text
  %install_ext https://raw.github.com/tkf/ipython-hierarchymagic/master/hierarchymagic.py
```

## `%importfile` magic

`%importfile` tries to import Python file in most "natural way". For
example, if you have `spam/egg/module.py`, `spam/egg/__init__.py`
and `spam/__init__.py`, you would want import `module.py` as
`spam.egg.module`, not as `module` or `egg.module`.
`%importfile` tries several heuristics to find the best "module path".

[Homepage](https://github.com/tkf/ipython-importfilemagic)

To install:

```text
  %install_ext https://raw.github.com/tkf/ipython-importfilemagic/master/importfilemagic.py
```

Usage:

```text
  %importfile PATH/TO/SOME/FILE.py
```

## Divers

Interactive plotting in notebook using the [flot library](http://code.google.com/p/flot/),
use the `ipython-flot` package.

[Homepage](https://github.com/crbates/ipython-flot/)

A Notebook example can be found [here](https://gist.github.com/3015819). This is not an extension, so it does not need `%install_ext` nor `%load_ext` to work.

## Mathematica

Mathematica can be integrated into notebooks using the `IPython-mathematicamagic-extension`. Install instructions and examples are included in a notebook on the GitHub page. Requires the mathlink python module distributed with Mathematica.

[Homepage](https://github.com/bjedwards/IPython-mathematicamagic-extension)

## Bitey

The Bitey extension adds a `%%bitey` cell magic for automatically compiling C (or C++) code into LLVM bitcode and loading the bitcode with [Bitey](https://github.com/dabeaz/bitey).  See also a [sample notebook](http://nbviewer.ipython.org/3458310/Bitey%20Magic.ipynb).

[Homepage](https://raw.github.com/gist/3458310/biteymagic.py)

To install:

```text
    %install_ext https://raw.github.com/gist/3458310/biteymagic.py
```

## Matlab 

The [Python MATLAB bridge](https://github.com/arokem/python-matlab-bridge)  enables calling of MATLAB code and functions from an IPython session and adds a `%%matlab` cell magic, which allows embedding matlab code in IPython notebooks. 

[Homepage](https://github.com/arokem/python-matlab-bridge)

Examples:

* [pymatbridge](http://nbviewer.ipython.org/urls/raw.github.com/arokem/python-matlab-bridge/master/pymatbridge.ipynb)
* [matlab magic](http://nbviewer.ipython.org/urls/raw.github.com/arokem/python-matlab-bridge/master/matlab_magic.ipynb) 

## py2tex

Enables conversion of simple expressions to a nice Tex Representation.

[Homepage](https://gist.github.com/4032651)

To install:

```text
    %install_ext https://raw.github.com/gist/4032651/c33b92cade4e899c81d03e1573a6f8107c7d470f/py2tex.py
```

An example is provided [here](http://nbviewer.ipython.org/urls/gist.github.com/raw/4040388/ee224a8e0875fad241cea4492b4408f1f72a1d8d/Example_py2tex.ipynb).

The [Unum class](https://bitbucket.org/kiv/unum/src>) is supported for unit-aware calculation.


## inumpy

A numpy extension for IPython. Autocomplete recarray.

[Homepage](https://github.com/piti118/inumpy)

To install:

```text
    %install_ext https://raw.github.com/piti118/inumpy/master/inumpy.py
```

##ipy_table

Create richly formatted data tables in IPython Notebooks.

[Homepage](http://epmoyer.github.com/ipy_table/)

Examples:

* [Introduction](http://nbviewer.ipython.org/urls/raw.github.com/epmoyer/ipy_table/master/ipy_table-Introduction.ipynb)
* [Reference Guide](http://nbviewer.ipython.org/urls/raw.github.com/epmoyer/ipy_table/master/ipy_table-Reference.ipynb)
