# IPython on GitHub

Notes on working with GitHub

## Milestones

* 100% of confirmed Issues should have a milestone
* An Issue should never be closed without a milestone.
* All issues closed should be marked with the next release milestone or **None**.
* Open Issues should only lack a milestone if:
    - more clarification is required (label: needs-info)
* In general, there will be three milestones with open Issues:
    - **next release**. This should be the default milestone of all issues.  As the release approaches, issues can be explicitly bumped to next release + 1.
    - **next release + 1**.  Only issues that we are confident will *not* be included in the next release go here.  This milestone should be mostly empty until relatively close
    - **wishlist**.  This is the milestone for issues that we have no immediate plans to address.
* The remaining milestone is **None**, which is used for Issues that are closed because they are:
    - Not actually an Issue (e.g. questions, discussion, etc.)
    - Duplicate of an existing Issue
    - closed as 'wontfix' (alternative: 'wontfix' milestone)
    - When an issue is closed with **None**, it means that the issue will not be fixed, or it was not an issue at all.
* When closing an issue, it should always have one of these two milestones:
    - **next release** because the Issue has been addressed
    - **None** because the Issue *will not* be addressed, or it is a duplicate/non-issue.

In general: When in doubt, mark with **next release**.  We can always push back when we have more specific plans for a given release.


## Labels and issues

Issues should always be labeled once they are confirmed (not necessary for issues that are still being clarified, or may be duplicates or not Issues at all).

Some significant labels:

* `needs-info`: issue needs more information from submitter before progress can be made
* `type-bug`: errors are raised, or unintended behavior occurs
* `type-enhancement`: improvements that are not bugs
* `backport-X.Y.Z`: Any fix for this issue should be backported to the maintenance branch.
* `prio-foo`: a priority level for ranking issues - nonessential, but prio-high/low are useful for explicitly promoting/demoting issues, particularly when nearing release.
* `ClosedPR`: This issue is a reminder for a PR that was closed for going stale.
* `quickfix`: Obvious or easy fixes.

All confirmed issues should at least have a `type-foo` label, and be marked with any affected components (e.g `parallel`, `qtconsole`, `htmlnotebook`), or particular sources of error (e.g. `py3k` or `unicode`).

The `doc` and `quickfix` labels are the best places for new contributors to start.


## Pull Requests

* All work is submitted via Pull Requests.
* Pull Requests can be submitted as soon as there is code worth discussing.  Pull Requests track the branch, so you can continue to work after the PR is submitted.  Review and discussion can begin well before the work is complete, and the more discussion the better.  The worse case is that the PR is closed.
* Pull Requests that have stalled should be closed (see [[our policy on closing PRs|Dev: Closing Pull Requests]]
* Pull requests should always be made against master (backporting PRs is described below).
* Pull Requests should be tested, if feasible:
    - bugfixes should include regression tests
    - new behavior should at least get minimal exercise

[Travis](http://travis-ci.org/#!/ipython/ipython) does a good job testing IPython and Pull Requests, but it may make sense to manually perform tests (possibly with our `test_pr` script), particularly for PRs that affect `IPython.parallel` or Windows.


## Opening an Issue

When opening a new issue, please take the following steps:

1. Search GitHub and/or Google for your issue to avoid duplicate reports.  Keyword searches for your error messages are most helpful.
2. If possible, try updating to master and reproducing your issue, because we may have already fixed it.
3. Try to include a minimal reproducible test case
4. Include relevant system information.  Start with the output of:

        python -c "import IPython; print(IPython.sys_info())"

   And include any relevant package versions, depending on the issue, such as matplotlib, numpy, Qt, Qt bindings (PyQt/PySide), tornado, web browser, etc.



## Backporting

* We should maintain an A.B.x maintenance branch for backporting
* That branch shall be called `A.B.x`, e.g. `0.13.x`, not `0.13.1`. This way, there is only one maintenance branch per release series.
* When an Issue is determined to be appropriate for backporting, it should be marked with the `backport-X.Y.Z` label.
* Any Pull Request which addresses a backport issue should also receive the same label.  This is the only case where we use labels for pull requests.
* Patches are backported to the maintenance branch by applying the pull request patch to the maintenance branch (currently with the [backport_pr](https://github.com/ipython/ipython/blob/master/tools/backport_pr.py) script).