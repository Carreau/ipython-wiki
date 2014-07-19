# Motivating cases

1. When sorting, it would better match the way we think about the code if you could define the key function within/after the call to `sorted()`, instead of having to define it before. Currently, this is only possible for a single expression in a lambda. Ideally, this function should also be in a throwaway scope, so that it is not accessible outside.
2. How would you implement something like `repeat(10, f(x))` (i.e. evaluating `f(x)` 10 times)? The options at present are all somewhat awkward: wrap it in a lambda (`repeat(10, lambda: f(x))`), pass a callable followed by its `*args` (`repeat(10, f, x)`), or put it in a string (`repeat(10, 'f(x)')`).
3. [Numba](http://numba.pydata.org/) compiles a subset of Python code using LLVM for performance. It currently uses [meta](https://pypi.python.org/pypi/meta) to *decompile* the byte code of a function into its AST. This is a really horrific hack around the lack of a real metaprogramming mechanism.
4. Representing expressions. [numexpr](https://github.com/pydata/numexpr) takes a string and parses it using `eval` (predates the `ast` module?). [Sympy](http://sympy.org/en/index.html) uses pre-declared variables which rebuild the expression by overloading operators done on them. It also has an IPython extension to automatically declare unknown variables, highlighting why that approach is awkward.
5. Parallel programming frameworks would like a way to do 'run this code on these engines'. Currently, you can only do this with code in a string (which is ugly), or with a function, which is awkward for defining global names (telling all engines to import something, for instance).

# Proposals

## PEP 403

[PEP 403](http://legacy.python.org/dev/peps/pep-0403/) proposes a decorator-like syntax, where `@in` is followed by a simple statement using the function defined below:

    @in sorted_list = sorted(original, key=f)
    def f(item):
        try:
            return item.calc_sort_order()
        except NotSortableError:
            return float('inf')

On the plus side, this adds minimal new syntax. However, some of us felt that the similarity to decorators is a bad thing, since it behaves quite differently (e.g. you have to use the function name, which you never do in normal decorators). It only really addresses the first case described above.

## PEP 3150

[PEP 3150](http://legacy.python.org/dev/peps/pep-3150/) proposes a 'given' clause which can be appended to many statements to add an indented block:

    sorted_data = sorted(data, key=?.sort_key) given:
        def sort_key(item):
            return item.attr1, item.attr2

This syntax feels more pythonic than PEP 403, but it would be trickier to implement. There is still no provision for examining the code itself: it is eagerly evaluated to produce a namespace. The 'rejected alternatives' section mentions using some token like `?given` to refer to the closure.

## Real inline functions

    sorted_list = sorted(original, key=inlinedef(x):
            try:
                return item.calc_sort_order()
            except NotSortableError:
                return float('inf')
        )  # <- This has to be dedented relative to the block

Any volunteers to write the code to parse this? ;-)

## Snowman blocks

This would allow a statement to get a following block as an object from which the AST is accessible. It's named for a joking suggestion to use the unicode snowman ☃ as the special token.

    run_on_engines(?)::
        import numpy
    
    repeat(10, ?)::
        a *= 2

    # Accessing .ns evaluates the block and gets the namespace
    sorted_list = sorted(original, key=?.ns.sort_key)::
        def sort_key(item):
            try:
                return item.calc_sort_order()
            except NotSortableError:
                return float('inf')
    
    # Alternative where sorted() accepts a block for key, and specifies that
    # the key will be called item in that block.
    sorted_list = sorted(original, key=?)::
        try:
            return item.calc_sort_order()
        except NotSortableError:
            return float('inf')

This is more powerful, because the code block itself can be used (not just the namespace from evaluating it), enabling neater solutions to cases 2-5 above.

But it is less convenient in cases where you just want a temporary function, as in case 1. As illustrated in these examples, either the statement must evaluate the block and pull a function from its namespace, or APIs must accept a block and make complex promises about how they will use it.

## AST-preserving decorator

A special decorator (or some other marker) would be recognised by the compiler, which would store the AST of the decorated object as an attribute on it:

```python

@ast_preserved
def foo(x, y):
    ...

import ast
for node in ast.walk(foo.__ast__):
    ...
```

This only really addresses case 3 above. It doesn't allow preserving the AST of simple expressions, only of functions or classes. And it doesn't make it any easier to reorder code as required in case 1.

## Context managers

Some people expect the `with` statement to be a much more flexible tool than it is, such that this would be possible:

```python
with repeat(10):
    print("hi")
```

This looks neat, but context managers are designed for something very different: running code before and after the indented block. The context manager does not have access to the block to inspect it, or to run it more than once. However, the design of context managers may contain inspiration for this problem.

## Function = signature + code

This is more the germ of an idea at the moment. A function is the combination of a signature, saying what arguments it takes, with a block of code that executes when it's called and possibly returns something. At present, you can only define these together. What would it look like if you could define signatures without code, and code without signatures, and then assemble them later?

In relation to the notion of signatures existing independently of callables, [PEP 362](http://www.python.org/dev/peps/pep-0362/) describes the function signature objects that are now part of the inspect module, and [PEP 457](http://www.python.org/dev/peps/pep-0457/) is a draft covering some additional details related to fulling supporting introspection of functions defined in C with positional only parameters. Also see the [Argument Clinic HOWTO](https://docs.python.org/3/howto/clinic.html) for a key motivating use case.

# In other languages

## Julia

Julia's [metaprogramming](http://julia.readthedocs.org/en/latest/manual/metaprogramming/) features include 'quoted' expressions, which represent their AST, instead of the result of evaluating them:

```julia
ex = :(a+b*c+1)
    
# Syntax for multi-line expressions
ex2 = quote
     x = 1
     y = 2
     x + y
  end
```

Macros are like functions for expressions, normally evaluated at parse time. It's also possible to use macros in quoted expressions, and expand them at run time. This also demonstrates interpolating one code expression into another:

```julia
macro until(expr1, expr2)
    quote
        while !($expr1)  # code interpolation
            $expr2
        end
    end
end

i = 0
@until i^2>30 begin
    println(i)
    i += 1
end
```

## Ruby

Ruby blocks are essentially anonymous functions, passed as the last argument to a function:

```ruby
numbers.map {|x| x*2}
```

In Python, the equivalent would be:

```python
map(numbers, lambda x: x*2)
```

[This post](http://stupidpythonideas.blogspot.com/2014/06/why-python-or-any-decent-language.html) argues that blocks are a poor solution to language limitations. It also proposes—not completely seriously—a syntax for multiline lambdas where they are the last argument in a function, by moving them outside the braces:

```python
sorted_list = sorted(original, key=*) lambda x:
   try:
       return item.calc_sort_order()
   except NotSortableError:
       return float('inf')
```

# Additional resources

Some other links that may be relevant:

* [Mython](http://mython.org/) (adds a "quote" statement to Python syntax)
* [AST Transformation Hooks](https://mail.python.org/pipermail/python-ideas/2011-April/009765.html)
* [Suite expressions](http://python-notes.curiousefficiency.org/en/latest/pep_ideas/suite_expr.html)
* 