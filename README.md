# Adam's git configuration files and utilities

This is my collection of configuration files and utilities for
[git](http://git-scm.com/).

## CONTENTS

Contents include:

### Upstream branch status reporting / management

* [`git-upstream`](https://github.com/aspiers/git-config/blob/master/bin/git-upstream) - obtain the upstream tracking branch (N.B. depends on `git-head`, `git-root`, and `git-prefix`)
* [`git-set-upstream`](https://github.com/aspiers/git-config/blob/master/bin/git-set-upstream) - set the upstream tracking branch (N.B. depends on `git-head` and `git-root`)
* [`git-mixdown`](https://github.com/aspiers/git-config/blob/master/bin/git-mixdown) - [mixdown](http://en.wikipedia.org/wiki/Audio_mixing_(recorded_music)) multiple branches into a single working branch.  Useful for testing a combination of bugfixes / features at once via a throw-away temporary working branch.
* [`git-mix`](https://github.com/aspiers/git-config/blob/master/bin/git-mix) - syntactic sugar for configuring `git mixdown`
* [`git-merged`](https://github.com/aspiers/git-config/blob/master/bin/git-merged) - show which branches are merged into upstream or the given commit-ish (N.B. depends on `git-root` and `git-upstream`)
* [`git-compare`](https://github.com/aspiers/git-config/blob/master/bin/git-compare) - compare two references to find out how far one is ahead/behind of the other
* [`git-compare-upstream`](https://github.com/aspiers/git-config/blob/master/bin/git-compare-upstream) - set the upstream tracking branch (N.B. depends on `git-compare`, `git-head`, and `git-upstream`, and hence also on `git-root` and and `git-prefix`)
* [`git-wip`](https://github.com/aspiers/git-config/blob/master/bin/git-wip) - returns true if the current repo has any work in progress, i.e. commits out of sync with upstream, or unmodified/untracked files
* [`git-rm-merged-orphan-branches`](https://github.com/aspiers/git-config/blob/master/bin/git-rm-merged-orphan-branches) - remove local branches which have been merged and are not in any other remote.  Use with care!!
* [`git-icing`](https://github.com/aspiers/git-config/blob/master/bin/git-icing) - a tasty wrapper around `git cherry` which adds a splash of colour, and blacklisting of commits which should never be upstreamed.
* [`git-cherry-menu`](https://github.com/aspiers/git-config/blob/master/bin/git-cherry-menu) - an interactive wrapper around `git icing` and `git notes` which makes it easy to cherry-pick and/or blacklist non-upstreamed commits.
* [`git-rnotes`](https://github.com/aspiers/git-config/blob/master/bin/git-rnotes) - a wrapper around `git notes` which makes it easier to share notes to and from remote repositories
* [`git-deps`](https://github.com/aspiers/git-deps) - **MOVED TO DEDICATED REPO** automatically detect dependencies between commits
* [`git-tag-patchset`](https://github.com/aspiers/git-config/blob/master/bin/git-tag-patchset) - quick way of tagging patch sets as they get uploaded to Gerrit, to keep track of a review's history, e.g. `git review && git tag-patchset`.

### History rewriting

* [`git-rewrite-author`](https://github.com/aspiers/git-config/blob/master/bin/git-rewrite-author) - rewrite the author for a range of commits
* [`git-rewrite-committer`](https://github.com/aspiers/git-config/blob/master/bin/git-rewrite-committer) - rewrite the committer for a range of commits
* [`git-sed-range`](https://github.com/aspiers/git-config/blob/master/bin/git-sed-range) - run a sed script over every log message in a range of commits
* [`git-add-prefix`](https://github.com/aspiers/git-config/blob/master/bin/git-add-prefix) - add a prefix string to every log message in a range of commits

### Management of remotes

* [`git-url-rewrite`](https://github.com/aspiers/git-config/blob/master/bin/git-url-rewrite) - convenient interface for setting [git URL rewrites via `url.$url.insteadOf`](http://qa-rockstar.livejournal.com/9961.html)
* [`git-annex-clean-sync`](https://github.com/aspiers/git-config/blob/master/bin/git-annex-clean-sync) - a wrapper around [`git annex sync`](https://git-annex.branchable.com/sync/) which avoids trying to touch ignored or unavailable remotes.

### Automatic committing and syncing

* [`git-auto-commit`](https://github.com/aspiers/git-config/blob/master/bin/git-auto-commit) -
  automatically commits files according to the `autocommit` [git
  attribute](https://git-scm.com/docs/gitattributes).  For example, if
  `.gitattributes` contains:

        *.org autocommit=min-age=+5m

  then any file ending in `.org` which is newly added (i.e. not in the
  index yet) or has unstaged changes, and whose last commit time and
  last mtime are both over 5 minutes, will be automatically staged and
  included in an automatic commit for this invocation of the commit.
  Files which have staged changes are assumed to be part of an
  unfinished manual commit process, and are therefore skipped.

* [`auto-commit-daemon`](https://github.com/aspiers/git-config/blob/master/bin/auto-commit-daemon) -
  a wrapper around
  [`git-auto-commit`](https://github.com/aspiers/git-config/blob/master/bin/git-auto-commit)
  to run it perodically.  This can be configured as a per-user systemd
  service, e.g. by placing the following in
  `~/.config/systemd/user/auto-commit-my-repo.service`:

        [Service]
        ExecStart=/bin/sh -c "/path/to/auto-commit-daemon /home/me/my/repo"
        Restart=always
        NoNewPrivileges=true
        SyslogIdentifier=auto-commit-my-repo
        Environment=SLEEP=1m

        [Install]
        WantedBy=default.target

* [`auto-sync-daemon`](https://github.com/aspiers/git-config/blob/master/bin/auto-sync-daemon) -
  a wrapper around
  [`git-annex-clean-sync`](https://github.com/aspiers/git-config/blob/master/bin/git-annex-clean-sync)
  which runs it whenever `master` or `synced/master` or
  `synced/git-annex` are updated.  When run across a network of
  remotes, it will keep the `master` branch in sync across all of
  them.  Works well in combination with setting
  `receive.denyCurrentBranch` to `updateInstead`, and using
  `git-safe-push-to-checkout` (see below) as the `push-to-checkout`
  hook.

* [`git-safe-push-to-checkout`](https://github.com/aspiers/git-config/blob/master/bin/git-safe-push-to-checkout) -
  a smarter `push-to-checkout` hook for when `receive.denyCurrentBranch`
  is set to `updateInstead`.  It's near-identical to git's default
  behaviour when no `push-to-checkout` hook is provided; however it
  additionally bails if we have emacs lockfiles indicating edits in
  progress for files which would be changed by the push-to-checkout.
  This means that push-to-checkout works more safely and doesn't
  rewrite files which are currently being edited in emacs with unsaved
  changes.  It can be installed via:

    ln -s `which git-safe-push-to-checkout` $my_repo/.git/hooks/push-to-checkout

### Mapping files/blobs back to commits

* [`git-ls-dir`](https://github.com/aspiers/git-config/blob/master/bin/git-ls-dir) -
  list files in a git repo tree together with the commits which most
  recently touched them ([see a
  screenshot](http://stackoverflow.com/a/8774800/179332))

* [`git-find-blob`](https://github.com/aspiers/git-config/blob/master/bin/git-find-blob) -
  find which commits contain a given (non-abbreviated) blob

### Sanity-checking submodule references

* [`git-find-missing-submodule-commits`](https://github.com/aspiers/git-config/blob/master/bin/git-find-missing-submodule-commits) -
  find dangling references to submodule commits

### Helper scripts

Little shell wrappers to make the git porcelain and higher-level scripts a little more beautiful.

* [`git-head`](https://github.com/aspiers/git-config/blob/master/bin/git-head) - obtain the current branch
* [`git-root`](https://github.com/aspiers/git-config/blob/master/bin/git-root) - obtain the absolute path to the root (top-level) directory of the repository we're currently in
* [`git-prefix`](https://github.com/aspiers/git-config/blob/master/bin/git-prefix) - obtain the current directory path relative to the root (top-level) directory of the repository
* [`git-cdup`](https://github.com/aspiers/git-config/blob/master/bin/git-cdup) - obtain the relative path from the current directory to the root (top-level) directory of the repository
* [`gfind`](https://github.com/aspiers/git-config/blob/master/bin/gfind) - like find(1), but only lists files tracked by git

### Shorthand wrappers

* [`gg*`](https://github.com/aspiers/git-config/tree/master/bin/) - a whole bunch of wrappers around standard git commands to reduce the number of keystrokes required and add a little bit of polish to the interface here and there.

### ... and more!

Feel free to [browse the `bin/` directory](https://github.com/aspiers/git-config/tree/master/bin) :-)

## INSTALLATION

This repository is designed to be
[stowed](http://www.gnu.org/software/stow/) directly into your home
directory:

    git clone git://github.com/aspiers/git-config.git
    stow -d . -t ~ git-config

However if you only want to cherry-pick bits and pieces then you can
easily just copy or symlink them in manually.  Just be aware that some
of the files depend on other files, some of which are in this
repository, some of which are in other repositories such as
https://github.com/aspiers/shell-env.

## LICENSE

The software in this repository is free software: you can redistribute
it and/or modify it under the terms of the GNU General Public License
as published by the Free Software Foundation, either version 3 of the
License, or (at your option) any later version.

This software is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.


[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/aspiers/git-config/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

