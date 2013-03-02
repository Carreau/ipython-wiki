We've learned a lot in our first 18 months with the message spec,
and we got a remarkable amount right.
But we didn't get everything right, and we want to clean some things up prior to 1.0.

A few names need to change, because they are Python-specific, and our message spec is not.

## `pyin`

When an `execute_request` arrives, a `pyin` message is published with the code about to be run.
This should not be a Python-specific name, such as `input` or `code` or ``.

- `pyin` should be renamed to something more generic,
  such as `input` or `code`, or `execute_content` or `execute_notification` or some such.


## `pyout`

`pyout` is also Python-specific, but has a more pressing issue - it is identical to `display_data`,
except with the addition of a `prompt_number` field. I propose *removing* `pyout`,
and adding the necessary information as a flag in `display_data`.
One possible implementation is to just add `prompt_number` to `display_data`,
where `prompt_number` is generally null, but has a value for current `pyout` cases.


## image shapes

Images currently cannot include shape information,
which means that one must use raw HTML to display an image with a specific shape.
This needs to change.  The `display_data` message already has a metadata field,
so the message spec does not need to change to display this information.
But there would need to be *implementation* changes.

Proposal: extend Python display spec,
such that `repr_foo` can *either* return raw mime-type data or a tuple of length 2.
If it is a tuple, the first element is the raw data,
and the second is its metadata.


## user\_expressions / user\_variables

The `user_expressions` and `user_variables` keys in an `execute_request`
currently only support plaintext reprs, and have bad custom representations of errors.
These should simply use the display protocol, and the `status: error` behavior used everywhere else.


## multiple objects

We currently have no native mechanism for presenting containers of objects (e.g. a list of images).
The only thing we can do is display each individual image via `map(display, list_of_images)`.
Nor can we have any representation of an object that might contain multiple elements
(such as a pair of images to be displayed side-by-side),
or objects for which multiple components *should* be displayed (e.g. LaTeX + PNG).
For all of these cases, the only option is to do the entire HTML rendering kernel-side,
and use raw HTML-reprs.  This sucks quite a bit, but I don't have a good answer for it.
Perhaps the JSON reprs / jsplugins are going to be the only way to do this sort of thing.
