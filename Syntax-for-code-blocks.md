What should the syntax be for a piece of parsed-but-unevaluated code in a Python module? This is complicated by the distinction between statements and expressions - the parser cannot handle statements nested within expressions, and it would apparently be very hard to make that work. This is also why multiline lambdas haven't happened.

Here are a few different ideas - there's plenty of room to refine and add to them:

## New compound statement

Like function and class definitions, a new statement could combine defining a block of code and binding it to a name:

```python
block foo:
    a + 1
    x = 2
```

## End-of-Statement

This syntax would only allow code blocks to be defined at the end of a statement, such as an assignment or a `return`:

```python

foo = ::
    a + 1
    x = 2
```

## Forward reference

Borrowing from [PEP 3150](http://legacy.python.org/dev/peps/pep-3150/), a special token could be used within arbitrary expressions to refer to a block defined afterwards. This allows one 'inline' code block per statement:

```python
res = simplify(?) block:
    sin(x)**2 + cos(x)**2

people[?] block:
    age > 10
```

## Special case expressions

There could be a separate syntax to 'quote' expressions, as opposed to whole code blocks. Quoted expressions could then be used inline, whereas blocks would have to be defined by one of the methods above. This is analogous to the distinction between full function definitions and lambdas.

```python
simplify(expr: sin(x)**2 + cos(x)**2)

people[expr: age > 10]
```

## Functions with the AST preserved

The compiler would recognise some special flag, such as a decorator, and attach the AST to the function object. This is a kind of 'metaprogramming lite'; it's more awkward for all the tools working with expressions if they have to be defined as a function.

```python
@ast_preserved
def f(a, b):
    c2 = a**2 + b**2
    return sqrt(c)

f.__ast__  # Is an ast.FunctionDef object
```

## Julia examples

For comparison, here's how Julia does it:

```julia
ex = :(a+1)  # Single line syntax - the : quotes the expression

ex2 = quote
        x = 1
        y = 2
        x + y
      end
```

In Julia, everything is an expression, so these can be both be used inline.