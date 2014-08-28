# Plans for the Fall 2014 Dev meeting at Berkeley

## Logistics

Location: we'll meet in Evans 481 ([Google maps](https://www.google.fr/maps/place/Evans+Hall,+University+of+California,+Berkeley,+Berkeley,+CA+94709,+USA/@37.8736514,-122.2577944,17z/data=!3m1!4b1!4m2!3m1!1s0x80857c24710a32ef:0x555901244b6a4c65)), not the same location as previous times. It's a slightly smaller room, but we should be OK.

Open House: Wednesday afternoon, 5-6:30pm.  Because of the different location, we don't have a hard cutoff like in Tolman.

## Attending

* Fernando Perez
* Brian Granger
* Min Ragan-Kelley (will miss Friday PM)
* Thomas Kluyver (will miss Tuesday)
* Paul Ivanov
* Matthias Bussonnier
* Kyle Kelley
* Aaron Culich
* LBL, NERSC and Stanford folks for the Thursday afternoon multiuser discussion.
* Adrienne Wantulok

## Agenda

First, a dump of main topics. We'll organize a more detailed breakdown below.

* Release plans for 3.0 with multiuser server.
  - How much can we realistically put out in 3.0?
  - Will fully configurable auth be ready? PAM, CalNet, Stanford, LDAP, ...?
  - Text editor? Terminal? ...
  - Scope limiting exercise on multiuser UX, before we go down the rabbit hole of implementing an entire
    desktop-in-the-browser or the new Django.
* Security: multiuser server, colaboratory, remote resources, etc. 
* Static widgets.
* Notebooks as files: status/needs on nbconvert; diff & merge, etc.
* Short brainstorm session on nbviewer.
* Support [Qt5 backend](http://matplotlib.org/1.4.0/users/whats_new.html#qt5-backend) for matplotlib.
* Documentation. 
  - Let's at least sort out our policy moving forward on notebooks, sphinx, JS, etc.  We may not have the resources for a big cleanup right away, but let's not make the holes deeper.
  - The 'big picture' of docs. I think there's three levels here:
    + 1) Better practices and model for the IPython/Jupyter docs. Tools, narrative vs reference, etc.
    + 2) From (1), what can we take that generalizes to get search across projects, etc. Ultimately when users are loading tools from the scipy stack, they should be able to ask their questions and get answers from all tools, without having to search through 10 separate sets of docs.
    + 3) How to make the above (1 and 2) generalizable to other ecosystems beyond the scipy stack. I'm particularly thinking of Julia here, which is fairly green-field on this topic and therefore an excellent opportunity to build a more cohesive doc system.
* Easy sharing of requirements and custom JS/CSS... Question appears both in nbviewer public contexts and JupyterHub usage.
* Logo Design
* PyCon talks?

### Tuesday

### Wednesday

* Open House

### Thursday
* 3-4:30pm Architecture discussion for possible IPython Notebook/[Berkeley Research Computing](http://research-it.berkeley.edu/brc) integration. Folks from LBL and NERSC may also attend.

### Friday