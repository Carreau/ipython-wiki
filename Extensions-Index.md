Extensions you can install for IPython are listed here. To create a new extension, please refer to the [extension docs](http://ipython.org/ipython-doc/dev/config/extensions/index.html).

## Asymptote

* [Homepage](https://github.com/jrjohansson/ipython-asymptote)

[Asymptote](http://asymptote.sourceforge.net/) is a powerful vector graphics
language for generating scientific diagrams. 

This IPython magic extension makes it easy to create and display Asymptote 
diagrams within an IPython notebook.

See the [example notebook](http://nbviewer.ipython.org/urls/raw.github.com/jrjohansson/ipython-asymptote/master/Asymptote-examples.ipynb) for installation instruction and example of how to use this magic extension.

## Django ORM magic

Define your django models in a cell and use them on the fly. Let the magic do the boring part.

* [Homepage](https://github.com/mgaitan/django-orm-magic)

Installation:

```
%install_ext https://raw.github.com/mgaitan/django-orm-magic/master/django_orm_magic.py
```

## ipythonPexpect magic

A magic that allows IPython notebooks to interface with other programs via Pexpect (written by Adam Lyon).
 
* [Example notebook](http://nbviewer.ipython.org/url/home.fnal.gov/~lyon/ipython_ext/ipythonPexpect_example.ipynb) - including interfacing with bash, R, and CERN's ROOT system.

To install :

```
%install_ext https://cdcvs.fnal.gov/redmine/projects/ipython_ext/repository/revisions/master/raw/ipythonPexpect.py
```


## fortran magic

Compile and import everything from a Fortran code cell, using f2py.

* [Homepage](https://github.com/mgaitan/fortran_magic)
* [Documentation](http://nbviewer.ipython.org/github/mgaitan/fortran_magic/blob/master/documentation.ipynb)

To install :

```
%install_ext https://raw.github.com/mgaitan/fortran_magic/master/fortranmagic.py
```

## ferret

ferret, the Data Visualisation and Analysis software from NOAA/PMEL, can now be integrated into notebooks using the `ipython-ferretmagic extension`. Install instructions and examples are included in notebooks on the GitHub page.

[Homepage](https://github.com/PBrockmann/ipython-ferretmagic)

To install:

```text
%install_ext https://raw.github.com/PBrockmann/ipython-ferretmagic/master/ferretmagic.py
```

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

* [pymatbridge](http://nbviewer.ipython.org/github/arokem/python-matlab-bridge/blob/master/pymatbridge.ipynb)
* [matlab magic](http://nbviewer.ipython.org/github/arokem/python-matlab-bridge/blob/master/matlab_magic.ipynb) 

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

* [Introduction](http://nbviewer.ipython.org/github/epmoyer/ipy_table/blob/master/ipy_table-Introduction.ipynb)
* [Reference Guide](http://nbviewer.ipython.org/github/epmoyer/ipy_table/blob/master/ipy_table-Reference.ipynb)


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

Run code cells in an iPython notebook until a breakpoint is encountered.

See:

https://github.com/ipython-contrib/IPython-notebook-extensions

## The AnyBody Modeling System

The AnyBody extension adds an `%%anybody` cell magic to run AnyScript macros directly from the IPython Notebook. The [AnyBody Modeling System](www.anybodytech.com) is a multibody modelling system for musculoskeletal biomechanics.

[Homepage](https://github.com/melund/anybodymagic)

To install: ``` %install_ext https://raw.github.com/melund/anybodymagic/master/anybodymagic.py ```

## Module version information: `%version_information`

The `%version_information` magic extension displays a table with version information for selected Python modules. Use it in a notebook keep a record of exactly which versions of dependency modules were used to run the notebook.

[Homepage](https://github.com/jrjohansson/version_information)

[Example notebook](http://nbviewer.ipython.org/github/jrjohansson/version_information/blob/master/example.ipynb)

## Section numbering: `%secnum`

Provides automatic section numbering for IPython notebooks.

Currently, level-1 headings are labelled like "1. First" and level-2 headings "1.1. Second".
The section numbers are automatically updated every time any cell changes type (e.g. from a Markdown cell to a heading cell).

[Homepage](https://github.com/dpsanders/ipython_extensions/tree/master/section_numbering)

To install:

    %install_ext https://github.com/dpsanders/ipython_extensions/tree/master/section_numbering

To use:

    %load_ext secnum
    %secnum

(Updates are automatic; `%secnum` only needs to be run once per session.)

## .zip, .tar.gz, .tgz extensions support: `%zip_extensions`

* [Homepage](https://github.com/mksenzov/ipython_zip_extensions)
* [Documentation](https://github.com/mksenzov/ipython_zip_extensions/blob/master/README.md)

To install:

    %install_ext https://raw.github.com/mksenzov/ipython_zip_extensions/master/zip_extensions.py

If for example you want to load/start using extension called `grasp` (described above) without using pip do:
    
    %load_ext zip_extensions

    %install_zip_ext https://pypi.python.org/packages/source/g/grasp/grasp-0.3.2.tar.gz
    %load_zip_ext grasp


## Graphviz Extensions:

These magic functions just pipe various input formats to `dot` using the standard library `subprocess` module. They were written for simplistic demonstration of binary decision diagrams in [PyEDA](https://github.com/cjdrake/pyeda).

* [Homepage](https://github.com/cjdrake/ipython-magic)

To install:

    %install_ext https://raw.github.com/cjdrake/ipython-magic/master/gvmagic.py

To load:

    %load_ext gvmagic

Magics:

* `%dot` - line/cell magic converts raw input to Graphviz `dot` SVG output.
* `%dotstr` - line magic converts string input to Graphviz `dot` SVG output.
* `%dotobj` - line magic converts object with `to_dot` method to Graphviz `dot` SVG output.
* `%dotobjs` - line magic converts sequence of objects with `to_dot` method to Graphviz `dot` SVG output.

Examples:

    %dot digraph G { a -> b; a -> c }

    %%dot digraph G {
        a -> b;
        b -> c;
    }

    %dotstr "digraph G { a -> b; a -> c }"

    %dotobj dotobj.to_dot()

    %dotobjs dotojb[0].to_dot(), dotobj[1].to_dot(), ...