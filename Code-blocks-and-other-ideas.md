# Motivating cases

1. When sorting, it would better match the way we think about the code if you could define the key function within/after the call to `sorted()`, instead of having to define it before. Currently, this is only possible for a single expression in a lambda.
2. How would you implement something like `repeat(10, f(x))` (i.e. evaluating `f(x)` 10 times)? The options at present are all somewhat awkward: wrap it in a lambda (`repeat(10, lambda: f(x))`), pass a callable followed by its `*args` (`repeat(10, f, x)`), or put it in a string (`repeat(10, 'f(x)')`).
3. [Numba](http://numba.pydata.org/) compiles a subset of Python code using LLVM for performance. It currently uses [meta](https://pypi.python.org/pypi/meta) to *decompile* the byte code of a function into its AST. This is a really horrific hack around the lack of a real metaprogramming mechanism.
4. Representing expressions. [numexpr](https://github.com/pydata/numexpr) takes a string and parses it using `eval` (predates the `ast` module?). [Sympy](http://sympy.org/en/index.html) uses pre-declared variables which rebuild the expression by overloading operators done on them. It also has an IPython extension to automatically declare unknown variables, highlighting why that approach is awkward.
5. Parallel programming frameworks would like a way to do 'run this code on these engines'. Currently, you can only do this with code in a string (which is ugly), or with a defined function, which is awkward for defining global names (telling all engines to import something, for instance).

