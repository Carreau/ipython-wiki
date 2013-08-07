| key            | value                                                       |
|----------------|-------------------------------------------------------------|
| Status         | Active                                                      |
| Author         | Matthias Bussonnier &lt;bussonniermatthias@gmail.com&gt;    |
| Created        | August 7, 2013                                              |
| Updated        | -                                                           |
| Discussion     |                                                             |
| Implementation |                                                             |


This is an IPep that propose to add a informal documentation for expected metadata field 
and their structure. 


# Metadata are optional

Unlike the rest of the fields of the `.ipynb` format which should be strictly
defined, metadata can support arbitrary json structure. Hence it is difficult
for third party plugin to know what they can write/read and what is the
expected structure of data they are suppose to handle.

This proposal will describe the use of cell-level metadata, but we keep in mind
that such a description could and shoudl also be done for the 2 others type of
metadata.

We remind the reader that the 3 type of metadata one can find are 

   - notebook level metadata.
   - cell  level metadata.
   - mimetype level metadata.

There is no worksheet level metadata as the worksheet structure will be remove in 2.0

# Name/tags

It has been decided for IPython notebook 2.0 to include two optional field in
metadata that could be expected by the html notebook and nbviewer. This propoal
will explain the goal of the `name` and `tags` field, as well as expose a few
good practice when readin/writing them. 

Thoses two fields will be added because of the need when converting notebook to
be able to distinguish individuql cell, qs well qs group of cells. This is not
needed for the correct use of the notebook, so such data belong in the metadata
filed of each cell.

## Name


The `name` field is suppose to hold a value which is a priory unique in a
notebook, and which should allow to reference a cell in a unique manner. As, if
present, it will mainly be used in user interface, it is recommended to be
meaningfull to the user easy to type and remember.

The naming of the key to `name` has been chosen in favor of `id` as it is more
meaningfull for user, and by the fact that `id` will be reserved as a mandatory
field that will not be user-modifiable. The concept of the `name` field can be
seen as the concept of `id` for html tags.


## reading/writing value

When readig those value, an implementation shoudl be able to make the
assumption that the `name` filed is unique across a notebook. When writing this
value to a cell metadata filed, implementation should do their best to avoid
aving duplicate `name`s across cell, eventually warning the user about
duplicatation and proposing an alternative. 

## allowed value.

The value of the name field is recommended to be a non-empty string.  If the
value is an empty string, or `undefined` implementation should be able to
assume that the field is non-existant or not set. If the value is other than a
string, implementation can also act as if the field was not set, but are
advised to do their best not to modify the current value unless explicitely
asked by user.

## allowed caracters. 

Do we stick to ascii ?  do we recommend not to use space ? try to avoid quotes ? sigle ? double? 

## case sensitivity

## Tags

list of string, 

each string avoid comma as often used as separator when user type.

Order don't count, 
each string to be unique
basically same recommendation as name, except for uniqueness?







