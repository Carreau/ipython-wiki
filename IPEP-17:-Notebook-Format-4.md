<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> Min RK &lt;benjaminrk@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> April 29, 2013</td></tr>
<tr><td> Updated </td><td> May 14, 2013</td></tr>
</table>

There are a few changes we need to make to the notebook that will not be backward compatible.
We do not intend to make these changes for 1.0, because nbformat changes are quite painful.
This is a catalog of the changes we intend to make when we do next rev the nbformat.

## remove multiple worksheets

The `worksheets` field is a list, but we have no UI to support multiple worksheets.
Our design has since shifted to heading-cell based structure,
so we never intend to support the multiple worksheet model.  The `worksheets` list of lists
shall be replaced with a single list, called `cells`.

## use mime-type output keys

We transform mimetype output data to short names, like `json` or `png`.
These should be restored back to proper mimetype values of `image/png` and `application/json`, etc.

## Remove python-centric names

Following [IPEP 13](IPEP-13:-Updating-the-Message-Spec), Python-specific keys in the message spec and notebook will be removed. Those affecting the notebook format:

- `pyout` will become `execute_output`
- `pyerr` will become `error`

## metadata changes

- remove notebook name from metadata
- move `language` key from code cells to top-level notebook metadata
- add kernel info to top-level notebook metadata in some form

