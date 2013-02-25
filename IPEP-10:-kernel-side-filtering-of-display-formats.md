## The issue

Currently, there is no mechanism to determine the display capabilities of the kernel,
and display formatters are all on by default in all circumstances.
This has the undesirable effect of suggesting that library code
check whether they are running in plain terminal IPython vs a Kernel
in order to make decisions about registering formatters.

For example, sympy [checks](https://github.com/sympy/sympy/blob/sympy-0.7.2/sympy/interactive/printing.py#L232)
`isinstance(get_ipython(), ZMQInteractiveShell)`
to determine whether it is in a Kernel or regular terminal IPython,
because it does not want to enable expensive latex printing that won't be used.

There is unavoidable ambiguity in the kernel case,
because there can be any number of frontends connected with various supported
display formats.
This IPEP does not propose that the kernel case should be changed,
rather a kernel-side specification of currently active formats
that can both serve to eliminate the cost of registering unused formatters
(which would eliminate the need to check in many cases),
and provide a direct capability-checking mechanism,
should it still be desired.


## Proposed implementation

- Add a `DisplayFormatter.active_types` list (configurable).

`DisplayFormatter.format` already has an `include` argument for specifying
which formats should be displayed.
The default behavior is to use every formatter in `DisplayFormatter.formatters`.
The proposed change is to use `active_types` as the default value for `include`,
with the effect of a global default whitelist of displayed formats.

- Limit terminal IPython to `text/plain`

The second part of the proposal is to set this `active_types` list to `['text/plain']`,
so that the extra formatters that may be registered but cannot be displayed
will not be called.

- deprecate `DisplayFormatter.plain_text_only`

Since this active_types is more general,
the `DisplayFormatter.plain_text_only` attribute is deprecated,
instead implying simply `active_types = ['text/plain']`

This approach should address the sympy case,
because there would no longer be a cost associated with registering an expensive latex formatter in basic terminal IPython, since it would never be called.


### Advantages

This change should eliminate the need to detect whether IPython is running in a Kernel or a plain terminal for display-capability reasons,
because there would no longer be a cost associated with registering unused formatting functions.
Further, if code still does want to check capabilities for some reason,
the `active_types` list can be checked, which actually described the display capabilities,
rather than the InteractiveShell subclass, as is done presently in sympy,
which only *implies* this sort of information.


### Disadvantages

The duplication of mime-type storage makes adding new mime-types more difficult.

Right now, if someone wants to register a PDF formatter,
they can simply do:

    formatter['application/pdf'] = MyPDFFormatter()

But adding `active_types` means this is a two-call process:

    formatter['application/pdf'] = MyPDFFormatter()
    formatter.active_types.append('application/pdf')

This can be mitigated by adding a `register_formatter` API that makes these two actions,
and adds any related logic as may arrive in the future (standard setter argument).


## Alternate approach

The `DisplayFormatter` already has a dict of formatters,
so the goal can also be achieved by actually removing unused formatters from the dict.
The existing behavior could remain the same, and the goal of avoiding
unused formats is still achieved.


### Advantages

- it would better support extending the supported mime-types list,
  by eliminating the information duplication of the `active_types` list.
  Adding `active_types` means that adding a new mime-type means
  adding it in two places: `DisplayFormatter.formatters` and `DisplayFormatter.active_types`.

### Disadvantages

- it requires checking for the existence of a formatter any time
- The cost of unregistering a formatter is much greater,
  because the object is actually removed.
  This would be disadvantageous for temporarily disabling a formatter,
  though I am not sure how valuable that use case is.
- much existing code would break, because people are already assuming that certain keys are present.
