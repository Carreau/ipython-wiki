This page is to help us to plan and record the development meeting that the IPython team will have the week of July 22nd, 2013 at UC Berkeley.

Note that this is not meant to be an open sprint for newcomers to IPython interested in learning about the project, instead it is a technical meeting to finish up the work from the first semester and plan out the next round.  But if you are interested in participating in that kind of in-depth discussion centered on design and implementation questions, we obviously welcome your participation.

If you are interested in learning more about IPython in general, meeting the team or bringing up new ideas about the project, we will hold an 'open house' on Wednesday, 4-6pm.  Feel free to stop by at that time for snacks and fun discussions.  If you have your own projects using IPython, by all means bring a short demo for all to see, as well as any feedback you may have for us.

# Logistics

The meeting will be held in [5101 Tolman Hall](http://www.berkeley.edu/map/maps/AB23.html) [Google Maps](http://goo.gl/maps/crBRT), and we will start every day at 10am, working til we drop.

# Topics to be covered

* Review the 1.0 roadmap and release and discuss items that didn't get done
* Develop the roadmap for the next release. This will include identifying the major tasks to be done along with the difficulty, priority and assignments
* Design discussion about nbconvert
  - How to best manage configurability and customization
  - How to integrate with the Notebook UI
  - What should the command line experience look like
* Design discussion about multidirectory support
  - Sessions, kernels and notebooks web services
  - URL scheme
  - Do we need a contents web service at this point
  - UI
* Interactive widgets discussion
  - Security approach
  - Low level message spec related aspects
  - Low level Python and JavaScript APIs
  - High level traits-like Python API
  - Streaming data
  - High level manipulate/interact API
  - Installation of extra JS code, aka "JS plugins"
  - Integration with plotting libraries
* UI/UX design questions
  - Width of Notebook div
  - Typography
  - Spacing of heading cells
  - Do we want to create an area for global widgets
* Other topics
  - Format of JavaScript docstrings

# Schedule

Monday

* Review 1.0 roadmap and release (Min)
* Outline work for 2.0, without trying to go into too much detail yet.
* Other-language kernels: Julia integration (FP)

Tuesday

* AM/PM: nbconvert design discussion (Brian/Jonathan)
* PM: Multidirectory capabilities (Brian/Zach)

Wednesday

* AM: Overview of interactive widgets, scope, goals
* AM: 10 minute **max** mini-talks by stake holders about their usage cases
* PM: Low level message spec aspects
* PM: Security and plugin approach
* 4-6pm: "Open house" for Berkeley campus community.

Thursday

* 10 AM: google hangout with Prabhu and other members of the India team.
* AM: Low level Python/JavaScript APIs
* PM: Traits-like API

Friday

* AM: Manipulate/interact API
* PM: Convert design ideas into a concrete roadmap for 2.0, with tasks, people and priorities (like we did for 1.0).

# Attending

1. Fernando Perez
2. Brian Granger
3. Min Ragan-Kelley
4. Paul Ivanov
5. Matthias Bussonnier
6. David Wolever
7. Zach Sailer
8. Jonathan Frederic
9. Steven Johnson (arrives Monday at noon, departs Friday morning)
10. Jeff Bezanson
11. Stefan Karpinski (arrives Sunday evening, departs Thursday evening)
12. Ana Nelson
13. Clayton Allen Davis (Wed-Fri)
14. Matthew Brett
15. Bob Dougherty (Thursday, maybe Wednesday)
16. David Sanders (Tues-Fri).

Attending remotely:
1. Prabhu Ramachandran (India, GMT+530)