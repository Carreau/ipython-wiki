<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Min RK &lt;benjaminrk@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> June 28, 2013</td></tr>
<tr><td> Updated </td><td> June 28, 2013</td></tr>
</table>

There are two pieces to [PEP 3148](http://www.python.org/dev/peps/pep-3148/):

- Executors
- Futures

In IPython, the analogs to these are Views and AsyncResults.
We should provide compatibility with these interfaces, as they are quite small,
and we already provide most of the functionality.

## 3148 APIs

### Future

- `cancel()` - we call this `abort()`, but this adds True/False return values.
- `cancelled()` - Return True if the call was successfully cancelled.
- `running()` - Return True if the call is currently being executed and cannot be cancelled.
- `done()` - We call this `ready()`.
- `result(timeout=None)` - We call this `get`.
- `exception(timeout=None)` - returns Exception object (currently stored in`AsyncResult._exception`).
- `add_done_callback(fn)` - add a callback to fire on the Future. This is the only real addition of functionality.

The only real new functionality is callbacks, and the only compatibility issue is that we already
have `result` as a property.

### Executor

- `submit(f, *args, **kwargs)` - we call this `apply`.
- `map(f, *iterables, timeout=None)` - we have `map`, but don't have a mechanism for timeout.
- `shutdown(wait=True)` - we have `shutdown`, but not `wait`.

## Implementation(s)

There are two logical choices for making these implementations:

1. Provide these APIs on the View
2. Provide separate Executor / Future classes, which wrap our implementation

Choice 1. has the advantage of not forking our interface, but there may be complications with subclassing,
as `concurrent.futures` is not in the stdlib for Python 2.
There would also be redundant method names, as many 

Choice 2. may be cleaner, in that it can simply raise if `concurrent.futures` is not available,
and always use simple subclasses. This would probably be provided as:

```python

executor = client.executor(targets=[...])
# or
executor = view.executor
```

and *possibly* adding (there may be complications preventing this)

```python
future = asyncresult.as_future()
```

but choice 2. would probably mean trading the extra metadata / multiplexing APIs for compatibility.
