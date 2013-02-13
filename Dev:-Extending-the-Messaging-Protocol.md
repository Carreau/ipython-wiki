My conception of representation protocol.

- You need to opt-in for a type of representation independently for each variable, 
- You need a fallback if the representation you want is not available.
- You might want several reprentation.

add this field to `execution_request`
`rich_user_variable : [list_of_desired_repr_variable]`

where each of `list_of_desired_repr_variable` is a dict with the following : 
```
{
    variable:'variable_name',
    prefered_repr: [list of [list of preferred repr format]]
}
```
example, I have a variable `gaussian` that I need :
 * as a `SVG`, if not possible as `PNG`, otherwise, as `JPG`.
 * also as `LaTeX`, or if not possible as `PlainText`

```
{
    variable:'gaussian',
    prefered_repr: [
                    ['svg','png','jpg'],
		    ['latex','text']
                   ]
}
```

The response should be a `dict` whose keys are variable name, and value are dict of `representation`.
representation would be a dict with key being repr type, and value dict of `status`, `error_reason` and `data`.

```javascrip
response : {
  gaussian : {
    'svg' : { 
      status:'error',
      data : null,
      error_reason, 'no svg repr for object'
    },
    'png' : { 
      status:'ok',
      data : '0123456789ABCDEF0123456789ABCDEF....',
      error_reason: null
    },
    'png' : { 
      status:'not_computed',
      data : null,
      error_reason: null
    },
    'latex' : { 
      status:'ok',
      data :'\LaTeX',
      error_reason: null
    },
    'text' : { 
      status:'not_computed',
      data : null,
      error_reason: null
    },
  },
  foo : {
    'text' : { 
      status:'error',
      data : null,
      error_reason: 'object does not exist'
    },
  }
}
```
