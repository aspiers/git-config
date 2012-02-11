# Adam's git configuration files and utilities

This is my collection of configuration files and utilities for
[git](http://git-scm.com/).

## CONTENTS

Contents include:

### Upstream branch status reporting / management

* [`git-upstream`](https://github.com/aspiers/git-config/blob/master/bin/git-upstream) - obtain the upstream tracking branch (N.B. depends on `git-head`, `git-root`, and `git-prefix`)
* [`git-set-upstream`](https://github.com/aspiers/git-config/blob/master/bin/git-set-upstream) - set the upstream tracking branch (N.B. depends on `git-head` and `git-root`)
* [`git-compare-upstream`](https://github.com/aspiers/git-config/blob/master/bin/git-compare-upstream) - set the upstream tracking branch (N.B. depends on `git-head` and `git-upstream`, and hence also on `git-root` and and `git-prefix`)

### Helper scripts

Little shell wrappers to make the git porcelain and higher-level scripts a little more beautiful.

* [`git-head`](https://github.com/aspiers/git-config/blob/master/bin/git-head) - obtain the current branch
* [`git-root`](https://github.com/aspiers/git-config/blob/master/bin/git-root) - obtain the absolute path to the root (top-level) directory of the repository we're currently in
* [`git-prefix`](https://github.com/aspiers/git-config/blob/master/bin/git-prefix) - obtain the current directory path relative to the root (top-level) directory of the repository
* [`git-cdup`](https://github.com/aspiers/git-config/blob/master/bin/git-cdup) - obtain the relative path from the current directory to the root (top-level) directory of the repository

### History rewriting

* [`git-rewrite-author`](https://github.com/aspiers/git-config/blob/master/bin/git-rewrite-author) - rewrite the author for a range of commits
* [`git-rewrite-committer`](https://github.com/aspiers/git-config/blob/master/bin/git-rewrite-committer) - rewrite the committer for a range of commits
* [`git-sed-range`](https://github.com/aspiers/git-config/blob/master/bin/git-sed-range) - run a sed script over every log message in a range of commits

### Management of remotes

* [`git-url-rewrite`](https://github.com/aspiers/git-config/blob/master/bin/git-url-rewrite) - convenient interface for setting [git URL rewrites via `url.$url.insteadOf`](http://qa-rockstar.livejournal.com/9961.html)

### and more

There are many other handy utilities - feel free to [browse the `bin/` directory](https://github.com/aspiers/git-config/tree/master/bin)!

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
