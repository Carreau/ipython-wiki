This document contains notes about the process that is used to release IPython.
Our release process is currently not very formal and could be improved.

Most of the release process is automated by the `release` script in the `tools`
directory of our main repository.  This document is just a handy reminder for
the release manager.

# 0. Environment variables

Set some env variables to note previous release tag and current release milestone, version, and git tag:

    export PREV_RELEASE=rel-1.0.0
    export MILESTONE=1.1
    export VERSION=1.1.0
    export TAG="rel-$VERSION"

# 1. Finish release notes

- merge any pull request notes into what's new:

        python tools/update_whatsnew.py

- update `docs/source/whatsnew/development.rst`, to ensure it covers the major points.
- move the contents of `development.rst` to `versionX.Y.rst`
- generate summary of GitHub contributions:

        python tools/github_stats.py --milestone $MILESTONE > stats.rst

  which may need some manual cleanup. Add the cleaned up result and add it to `docs/source/whatsnew/github-stats-X.Y.rst` (make a new file, or add it to the top, depending on whether it is a major release).
  You can use:

        git log --format="%aN <%aE>" $PREV_RELEASE... | sort -u -f

  to find duplicates and update `.mailmap`.

# 2. Run the `build_release` script

This does all the file checking and building that the real release script will do.
This will let you do test installations, check that the build procedure runs OK, etc.
You may want to also do a test build of the docs.

# 3. Create and push the new tag

Edit `IPython/core/release.py` to have the current version.

    python setup.py jsversion
    git commit -a -m "release $VERSION"
    git tag -a -m "release $VERSION" "$TAG"
    git push origin master --tags

# 4. Get a fresh clone of the tag for building the release:

    cd /tmp
    git clone --recursive https://github.com/ipython/ipython.git -b "$TAG"

# 5. Run the `release` script

This makes the tarballs, zipfiles, and wheels.  It posts them to archive.ipython.org and
registers the release with PyPI.

This will require that you have current wheel, Python 3.3 and Python 2.7, and an appropriate version of LESS.

# 6. Publish updated docs

For this we are now using GitHub pages:

    cd docs
    V=[MAJOR_VERSION_ONLY] make gh-pages

- Update the `stable` symlink to point to the released version.
- Check that the output makes sense
- Run `git push` to update the public version of the docs on gh-pages.

# 7. Update the IPython website

- release announcement
- update stable and download links
- update links on the documentation page

# 8. Drafting a short release announcement

This should include i) highlights and ii) a link to the html version of
the *What's new* section of the documentation.

Post to mailing list, and link from Twitter.

# 9. Celebrate!
