This document contains notes about the process that is used to release IPython.
Our release process is currently not very formal and could be improved.

Most of the release process is automated by the `release` script in the `tools`
directory of our main repository.  This document is just a handy reminder for
the release manager.

# 1. Generate release notes.

For writing release notes, the following will cleanly show who contributed as author
of commits (get the previous release name from the tag list with `git tag`):

    git log --format="* %aN" $PREV_RELEASE... | sort -u

You can use:

    git log --format="%aN <%aE>" $PREV_RELEASE... | sort -u -f

To find duplicates and update `.mailmap`.

# 2. Run `build_release`

This does all the file checking and building that the real release script will do.
This will let you do test installations, check that the build procedure runs OK, etc.
You may want to disable a few things like multi-version RPM building while testing, because
otherwise the build takes really long.

# 3. Run the `release` script

This makes the tar.gz, eggs and Win32 `.exe` installer.  It posts them to the site and
registers the release with PyPI.

# 4. Update the IPython website

This should include announcements and links to the updated release notes
in html form. Remember to put a short note on the news page of the site.
   
# 5. Drafting a short release announcement

This should include i) highlights and ii) a link to the html version of
the *Whats new* section of the documentation.

# 6. Make sure that the released version of the docs is live on the site.

For this we are now using the gh-pages system:

- Make a static directory for the final copy of the release docs.
- Update the `index.rst` file and run `build_index.py` to update
  the html version.
- Update the `stable` symlink to point to the released version.
- Run `git add` for all the new files and commit.
- Run `git push` to update the public version of the docs on gh-pages.

# 7. Celebrate!
