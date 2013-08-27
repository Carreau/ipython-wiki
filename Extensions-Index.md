Extensions you can install for IPython are listed here. To create a new extension, please refer to the [extension docs](http://ipython.org/ipython-doc/dev/config/extensions/index.html).

## ipyBibtex

With this extension you can use LATEX style references within IPython (Notebook) while still using markdown for formatting.

[Homepage](https://gist.github.com/z-m-k/6080008)

[Example Notebook](http://nbviewer.ipython.org/6080008/ipyBibtex.ipynb)

To install:

```text
%install_ext https://gist.github.com/z-m-k/6080008/raw/7611cb869b5ff162d00e95856054b887b0b0d095/ipyBibtex.py
```


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

## Bitey

The Bitey extension adds a `%%bitey` cell magic for automatically compiling C (or C++) code into LLVM bitcode and loading the bitcode with [Bitey](https://github.com/dabeaz/bitey).  See also a [sample notebook](http://nbviewer.ipython.org/3458310/Bitey%20Magic.ipynb).

[Homepage](https://raw.github.com/gist/3458310/biteymagic.py)

To install:

```text
    %install_ext https://raw.github.com/gist/3458310/biteymagic.py
```
## Mathematica

Mathematica can be integrated into notebooks using the `IPython-mathematicamagic-extension`. Install instructions and examples are included in a notebook on the GitHub page. Requires the mathlink python module distributed with Mathematica.

[Homepage](https://github.com/bjedwards/IPython-mathematicamagic-extension)

## Matlab 

The [Python MATLAB bridge](https://github.com/arokem/python-matlab-bridge)  enables calling of MATLAB code and functions from an IPython session and adds a `%%matlab` cell magic, which allows embedding matlab code in IPython notebooks. 

[Homepage](https://github.com/arokem/python-matlab-bridge)

Examples:

* [pymatbridge](http://nbviewer.ipython.org/urls/raw.github.com/arokem/python-matlab-bridge/master/pymatbridge.ipynb)
* [matlab magic](http://nbviewer.ipython.org/urls/raw.github.com/arokem/python-matlab-bridge/master/matlab_magic.ipynb) 

## IDL

Provides magics for embedding [IDL](http://www.exelisvis.com/ProductsServices/IDL.aspx) and [GDL](http://gnudatalanguage.sourceforge.net/) code using [pIDLy](https://github.com/anthonyjsmith/pIDLy).

[Homepage](https://github.com/ebellm/ipython-idlmagic)

To install:

```text
%install_ext https://raw.github.com/ebellm/ipython-idlmagic/master/idlmagic.py
```

## py2tex

Enables conversion of simple expressions to a nice Tex Representation.

[Homepage](https://gist.github.com/4032651)

To install:

```text
    %install_ext https://raw.github.com/gist/4032651/c33b92cade4e899c81d03e1573a6f8107c7d470f/py2tex.py
```

An example is provided [here](http://nbviewer.ipython.org/urls/gist.github.com/raw/4040388/ee224a8e0875fad241cea4492b4408f1f72a1d8d/Example_py2tex.ipynb).

The [Unum class](https://bitbucket.org/kiv/unum/src>) is supported for unit-aware calculation.

## duster

Reset namespace and automatically (re)load several modules immediately thereafter.

[Homepage](https://github.com/lebedov/duster)

To install (from the command line):

```text
    pip install duster
```

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


## SQLite magics

IPython magics to run SQL and see the result in table format.

[Homepage](https://github.com/tkf/ipython-sqlitemagic)

```text
%install_ext https://raw.github.com/tkf/ipython-sqlitemagic/master/sqlitemagic.py
```


## Doctest magics

Run and debug doctest in IPython.

[Homepage](https://github.com/tkf/ipython-doctestmagic)

```text
%install_ext https://raw.github.com/tkf/ipython-doctestmagic/master/doctestmagic.py
```


## Magics for temporary workspace

- `%cdtemp` -- Creates a temporary directory that is magically cleaned up
  when you exit IPython session.

- `%%with_temp_dir` -- Run Python code in a temporary directory and
  clean up it after the execution.

[Homepage](https://github.com/tkf/ipython-tempmagic)

```text
%install_ext https://raw.github.com/tkf/ipython-tempmagic/master/tempmagic.py
```


## Magics for activity, block, network and sequence diagrams

Create activity, block, network and sequence diagrams using text definitions similar to graphwiz.
The extension uses blockdiag modules from http://blockdiag.com

[Homepage](https://bitbucket.org/vladf/ipython-diags)

```text
%install_ext https://bitbucket.org/vladf/ipython-diags/raw/default/diagmagic.py
```

## sql

Passes SQL statements to any database reachable by SQLAlchemy, and renders results as tabular HTML or pretty text tables, where possible.

[Homepage](https://pypi.python.org/pypi/ipython-sql)

[Usage sample](http://nbviewer.ipython.org/5272681)

To install (from command line):

```pip install ipython-sql```

## grasp

A set of python functions to help with interactive object
inspection and discovery.

 * %gist -- object inspection
 * %rtype -- recursive type, deep object inspection
 * %apropos -- deep search for things with a given name, value, etc.

Install: ```pip install grasp```

Use: ```%load_ext grasp```

Source code: http://launchpad.net/grasp

More info: http://pypi.python.org/pypi/grasp/

## breakpoint

Run code cells in an iPython notebook until a breakpoint is encountered

Extension download: https://gist.github.com/juhasch/5241322

Demo notebook: https://gist.github.com/juhasch/5287081

(You can't see the breakpoints using the nbviewer: http://nbviewer.ipython.org/5287081 )

## The AnyBody Modeling System

The AnyBody extension adds an `%%anybody` cell magic to run AnyScript macros directly from the IPython Notebook. The [AnyBody Modeling System](www.anybodytech.com) is a multibody modelling system for musculoskeletal biomechanics.

[Homepage](https://github.com/melund/anybodymagic)

To install: ``` %install_ext https://raw.github.com/melund/anybodymagic/master/anybodymagic.py ```

## Module version information: `%version_information`

The `%version_information` magic extension displays a table with version information for selected Python modules. Use it in a notebook keep a record of exactly which versions of dependency modules were used to run the notebook.

[Homepage](https://github.com/jrjohansson/version_information)

[Example notebook](http://nbviewer.ipython.org/urls/raw.github.com/jrjohansson/version_information/master/example.ipynb)


