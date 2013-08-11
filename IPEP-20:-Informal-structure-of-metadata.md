| key            | value                                                       |
|----------------|-------------------------------------------------------------|
| Status         | Active                                                      |
| Author         | Matthias Bussonnier &lt;bussonniermatthias@gmail.com&gt;    |
| Created        | August 7, 2013                                              |
| Updated        | -                                                           |
| Discussion     |                                                             |
| Implementation |                                                             |


This IPEP proposes to add informal documentation for expected metadata fields 
and their structure. 


# Metadata are optional

Unlike the rest of the fields of the `.ipynb` format, which should be strictly
defined, metadata can support an arbitrary JSON structure. Hence, it is difficult
for third-party plugins to know what they can write/read and what the
expected structure of data is that they are supposed to handle.

The 3 types of metadata available are:

   - notebook-level metadata;
   - cell-level metadata;
   - mimetype-level metadata.

[Note that there is no worksheet-level metadata, since the worksheet structure will be removed in 2.0.]

This proposal describes the use of cell-level metadata, but we should bear in mind 
that such a description should also be done for the 2 other types of metadata.


# Cell metadata fields: `Name` and `tags`

For IPython Notebook 2.0, it has been decided to include two optional fields in
metadata that could be expected by the Notebook and NbViewer, `name` and `tags`. The current proposal
will explain the goal and usage of the `name` and `tags` fields, as well as a few
good practices when reading/writing them. 

These two fields will be added because of the need when converting a notebook to
be able to identify an individual cell, as well as groups of cells. This is not
needed for the correct use of the notebook, so such data belong in the metadata
field of a given cell.

## Name

The `name` field is designed to contain a value which is a priori unique in a
notebook, and which should allow to reference a cell in a unique way. This
field (if present) will mainly be used in the user interface, so it is recommended to be
meaningful to the user, easy to type and easy to remember.

The naming of the key as `name` was chosen in favor of `id` as it is more
meaningful for the user; `id` is reserved for future usage, as a mandatory
field that will not be user-modifiable. The concept of the `name` field is
similar to that of `id` for HTML tags.


### Reading/writing values

When reading these values, an implementation should be able to make the
assumption that the `name` field is unique across a notebook. When writing this
value to a cell metadata field, implementations should do their best to avoid
having duplicate `name`s across cells, warning the user about
duplication and proposing an alternative. 

### Allowed value

The value of the name field should be a non-empty string.  If the
value is an empty string or `undefined`, an implementation is able to
assume that the field is non-existent or not set. If the value is 
something other than a string, implementations can also act as if 
the field was not set, but are advised to do their best not to modify
the current value unless explicitly requested by the user.

### Allowed characters. 

Do we stick to ASCII? Do we recommend not to use spaces? Try to avoid quotes? -- single? double? 

### Case sensitivity

## `tags`

Tags are:
- a list of strings

with the following properties:

- each string avoids commas, since these are often used as separators when a user types.
- the order doesn't count
- each string must be unique
- basically the same recommendation as for `name`, except for uniqueness?






