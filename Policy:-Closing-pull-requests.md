IPython has the following policy on closing pull requests.  The goal of this policy is to keep our pull request queue small and allow us to focus on code that is being actively developed and has a strong chance of being merged in master soon.

A pull request will be closed when:

* It has been reviewed, but has sat for a month or more waiting for the submitter to commit more code to address the comments.
* The review process has uncovered larger design or technical issues that extend beyond the details of the specific pull request.

In general we will not close pull requests because of a lack of review.  If a pull request has sat for a month or more without review, we need to kick ourselves and get to reviewing it.

When a pull request is closed we will do the following:

* Post a github message to the pull request to confirm that everyone is fine with closing the pull request. This message should cite this policy.
* Open an issue to track the pull request. This issue should describe what would be needed in order to reopen the pull request.
* Post a github message to the pull request encouraging the submitter to continue with the work and detail what issues need to be addressed in order for the pull request to be reopened.

This policy was discussed in the following thread:

http://mail.scipy.org/pipermail/ipython-dev/2012-August/010025.html

Example Message:
================

```text
Hi, 
This PR is without activities for 1 month now, 
I'm going to close it and open an issue to reference it. 
This does not mean than we refuse the code that is here, but that we try to keep the number of opened Pull request as low as possible to have better bird view of active code.

Feel free to reopen this PR whenever you want or contact us if you have any questions.

Thanks for contributing.

see https://github.com/ipython/ipython/wiki/Policy%3A-Closing-pull-requests/ for our policies on closing pull request.
```
