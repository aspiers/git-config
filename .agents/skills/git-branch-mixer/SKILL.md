---
name: git-branch-mixer
description: Manage git branch mixes and run mixdowns using ggmx and ggmxd. Use when the user says things like "add this branch to the mix", "remove a branch from the mix", "show the mix", "mix down the branches", or "run a mixdown".
disable-model-invocation: true
---

# Git Branch Mixer

`ggmx` (`git mix`) configures named sets of branches to merge together.
`ggmxd` (`git mixdown`) executes a mixdown — resetting a target branch to a
base and merging all the other branches in.

The typical use case is testing a combination of feature/bugfix branches
together via a throw-away temporary working branch.

**Critical rule:** mixdown target branches (especially `working`) are local
throw-away integration branches. Never push a mixdown target branch, create a PR
from it, or otherwise publish it unless the user explicitly asks for that exact
action. Independent source branches may be pushed as needed, but the mixed
working branch stays local by default.

**Default location:** when producing a mixdown for the `working` branch, run it
from the repository's main checkout, not from a linked worktree, unless the user
specifies another location. The main checkout is normally where the combined
software is built, run, and tested; source branches may still live in linked
worktrees. Because the mixdown checks out and resets `working`, stop and ask
before stashing or otherwise moving aside changes from a dirty main checkout.

## Configuring mixes with `ggmx`

```bash
ggmx                          # show all configured mixes
ggmx <target>                 # show branches in mix for <target>
ggmx <target> A B C           # set mix for <target> to branches A, B, C
                               # (first branch is the base)
ggmx [<target>] +D [-B]       # add D to / remove B from mix for <target>
                               # (defaults to 'working' if <target> omitted)
ggmx -d <target>              # delete the mix for <target>
```

Mixes are stored in git config under `mixdown.<target>`.
You can also edit them directly with `git config --edit`.

## Running a mixdown with `ggmxd`

```bash
ggmxd                          # run mixdown using config for default target ('working')
ggmxd -b <target>              # run mixdown using config for <target>
ggmxd BASE B1 [B2 ...]         # explicit mixdown: reset to BASE, merge B1, B2...
ggmxd -b <target> BASE B1 ...  # explicit mixdown into named target branch
```

**Always pass `-c` / `--checkout`** to leave the target branch checked out
after the mixdown, ready for testing:

```bash
ggmxd -c                       # mixdown from config, stay on target branch
ggmxd -c BASE B1 B2            # explicit mixdown, stay on target branch
```

Without `-c`, `ggmxd` restores your previously checked-out branch after
completing the mixdown. Use this when you want to rebuild the mix in the
background without switching away from your current branch.

After a successful mixdown, if the combined software can be run or otherwise
tested, rebuild it and restart the running instance from the main checkout
before reporting it ready. A mixdown that has not reached the user's actual
test environment is not complete.

### Other options

```bash
ggmxd -s STRATEGY              # set merge strategy (default: octopus)
                               # use e.g. -s recursive for sequential merges
                               # with conflict resolution shells between each
```

## After changing a mix

After any `ggmx` operation that modifies a mix (adding, removing, or setting
branches), always ask the user whether they want to run a mixdown now.

## Typical workflow

```bash
# One-time setup: configure the mix for the 'working' target branch
ggmx working main feature/foo bugfix/bar

# Add another branch later
ggmx +feature/baz

# Run the mixdown and stay on the result for testing
ggmxd -c

# Rebuild after rebasing one of the branches
ggmxd -c
```
