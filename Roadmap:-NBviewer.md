# NBviewer Roadmap

Future plan for nbviewer.

## Requirement

Finish nbconvert, probably have it merge into ipython or self installable.

## Planning

 - Think of the url scheme, and the services we want to expose.
 - Think about caching/ and the frameworks we want to use (keep flask? look at Flask Blueprint.) 
 - Open a google analytics account ?
 - Do we start storing data about users ?

## Actual things to do in order. 

 - 0 - Fix the annoying bugs (404/500 cached) 
 - 1 - move to mongodb (free postgres is too limited) 
 - 2 - Use the new NBconvert class

 - 3 - Write a new jinja template for nbconvert.
    - that does only include notebook body
    - include the notebook css in the header of nbviewer itself
 - 4 Include boostrap menubar with different actions
    - share on twitter, fork, share on G+


## Other stuff I want to do 
  
 - Licence in footer (nb extension + metadata)
 - Authors in footer (nb extension + metadata) 
 - Use twitter "cards" and Google+ "snippet"
 - Integrate small screencapture tool using my boiling-chamber heroku app
 - List all user gists that are ipynb files
 - List the more popular/viewed ipynb files
 

## Discussion on few point : 

  - subdomain vs url for different services :
    - more robust to faillure.
    - independant update
    - load balance traffic
    - less expensive
    - Could we share some cache and data ?
  - url scheme : 
    - / main page
    - /[prefix]/, some [prefix] should be reserved at our usage.
        - static
        - faq
        - contact
        - url
        - urls
        - help
        - github?
        - gist?
        - others?
     - /[numbers], keep for backward compatibility with before
     - /u/[username]/ list of user gist
     - /g/[username]/ list of user repos (browsable) 
     - /u/[username]/number corresponding gist if tree, browsable if blob
   - some place to download bookmarklet/notebook extensions


 

    