# AI agent guidance

This file provides guidance to AI agents like Claude Code, Gemini CLI,
Cursor, opencode etc. when working with code in this repository.

## Repository Overview

This is Adam Spiers' personal git configuration repository - a mature
collection of 100+ git utilities, scripts, and configuration files
designed to enhance git workflows. The repository is designed to be
deployed with GNU Stow into the home directory.

## Development Commands

### Testing

```bash
# Test individual scripts directly (no formal test framework)
./bin/git-auto-commit --help
./bin/git-mixdown --dry-run
./bin/git-upstream

# Test core helper functionality
./bin/git-root
./bin/git-head
./bin/git-prefix
```

### Deployment

```bash
# Deploy configuration (creates symlinks to home directory)
stow -d . -t ~ git-config

# Remove deployment
stow -d . -t ~ -D git-config
```

### Key Commands

```bash
# Automated commits with gitattributes integration
./bin/git-auto-commit                     # Auto-commit eligible files
./bin/git-auto-commit --dry-run          # Show what would be committed
./bin/git-auto-commit --debug            # Verbose output

# Multi-branch management
./bin/git-mixdown                        # Mix multiple branches for testing
./bin/git-mix                            # Configure mixdown
./bin/ggrbm                              # Rebase multiple branches

# Advanced cherry-picking workflow
./bin/git-icing                          # Color-coded git cherry with blacklisting
./bin/git-cherry-menu                    # Interactive cherry-pick/blacklist interface

# Upstream tracking and comparison
./bin/git-upstream                       # Get upstream tracking branch
./bin/git-compare-upstream               # Compare with upstream
./bin/git-wip                           # Check for work in progress
```

## Architecture

### Core Script Dependencies

The repository follows a **layered architecture** where higher-level
tools depend on foundation scripts:

- **Foundation Layer**: `git-head`, `git-root`, `git-prefix`, `git-cdup` - basic repository information
- **Utility Layer**: `git-upstream`, `git-compare` - builds on foundation scripts
- **Workflow Layer**: `git-mixdown`, `git-icing`, `git-auto-commit` - complex operations using utility layer

### Key Components

#### Automated Commit System (`git-auto-commit`)

- **Language**: Python 3 with pygit2 dependency
- **Configuration**: Uses `.gitattributes` with custom `autocommit` attribute
- **Example**: `*.org autocommit=min-age=+5m` auto-commits .org files after 5 minutes
- **Features**: Time-based policies, dry-run mode, staged file detection

#### Multi-Branch Workflow Tools

- **`git-mixdown`**: Creates temporary branches combining multiple feature branches
- **`git-mix`**: Configuration helper for mixdown operations
- **`ggrbm`**: Automated rebasing of multiple branches as single workflow

#### Cherry-Pick Enhancement (`git-icing`)

- **Language**: Ruby with color support
- **Features**: Visual commit status, blacklisting with git notes integration
- **Workflow**: Used with `git-cherry-menu` for interactive commit management

#### Shorthand Wrappers (`gg*` family)

- **Pattern**: 80+ scripts like `gga` → `git add`, `ggst` → `git stash`
- **Purpose**: Reduce keystrokes while maintaining full git functionality
- **Implementation**: Simple `exec git <command> "$@"` pattern

### Configuration-Driven Architecture

#### Git Attributes Integration

The repository extensively uses `.gitattributes` for:
- Custom diff drivers for multiple languages (bibtex, cpp, python, ruby)
- Automated commit policies via `autocommit` attribute
- Language-specific file handling

#### Stow-Based Deployment

- Designed for GNU Stow deployment: `stow -d . -t ~ git-config`
- `.stow-local-ignore` controls which files are excluded from deployment
- Symlinks configuration files directly into home directory structure

### Language Distribution and Patterns

- **Bash/Shell**: ~95% of scripts (simple wrappers and complex workflows)
- **Python 3**: `git-auto-commit` (uses pygit2 for git operations)
- **Ruby**: `git-icing` (complex color output and cherry-pick logic)
- **Zsh**: `auto-commit-daemon` (service wrapper)

### Notes-Based Workflow Integration

Several scripts use git notes for enhanced functionality:
- **Commit blacklisting**: Notes mark commits that should never be upstreamed
- **TODO tracking**: Notes system for commit management
- **`git-rnotes`**: Sharing notes between repositories
- **Integration**: Works with `git-icing` and `git-cherry-menu`

## Dependencies

### System Requirements

- GNU Stow for deployment
- Standard git installation
- Python 3 with pygit2 library (for git-auto-commit only)
- Ruby interpreter (for git-icing only)
- Standard UNIX tools (bash, zsh, sed, awk)

### Dependency Philosophy

- **Minimal external dependencies**: Uses system tools rather than heavy frameworks
- **Self-contained**: Most functionality implemented without external libraries
- **Modular**: Each script has single responsibility but can be combined

## Important Development Notes

### Personal Configuration Focus

- Heavily customized for specific workflows and hardware setups
- Not designed as general-purpose tools for distribution
- Consider cherry-picking individual scripts rather than wholesale adoption
- Many scripts depend on other scripts in this repository

### Code Standards

- Extensive help text and usage examples in script headers
- Consistent error handling patterns across bash scripts
- GPL v3 licensed with proper copyright notices throughout
- Self-documenting code with meaningful variable names

### Testing Philosophy

- No formal test framework - relies on real-world usage
- Scripts include `--help` and `--dry-run` options where applicable
- Test individual scripts directly during development
- Mature codebase with extensive production usage

## Beads Solo

Use the `beads-solo` skill for Beads setup and maintainer policy in this
repository. Use the `beads` skill for the standard Beads workflow.

This repository opts into the Beads **team-maintainer** profile for issue
management, commits, and Git pushes. Unless a current user or orchestrator
instruction says otherwise, agents may manage issues, make atomic commits,
and push Git branches as work progresses. They must not sync or push Dolt
state unless explicitly requested.

<!-- BEGIN BEADS INTEGRATION v:1 profile:minimal hash:1105d646 -->
## Beads Issue Tracker

This project uses **bd (beads)** for issue tracking. Run `bd prime` to see full workflow context and commands.

### Quick Reference

```bash
bd ready              # Find available work
bd show <id>          # View issue details
bd update <id> --claim  # Claim work
bd close <id>         # Complete work
```

### Rules

- Use `bd` for ALL task tracking — do NOT use TodoWrite, TaskCreate, or markdown TODO lists
- Run `bd prime` for detailed command reference and session close protocol
- Use `bd remember` for persistent knowledge — do NOT use MEMORY.md files

**Architecture in one line:** issues live in a local Dolt DB; sync uses `refs/dolt/data` on your git remote; `.beads/issues.jsonl` is a passive export. See https://github.com/gastownhall/beads/blob/main/docs/core-concepts/sync-concepts.md for details and anti-patterns.

## Agent Context Profiles

The managed Beads block is task-tracking guidance, not permission to override repository, user, or orchestrator instructions.

- **Conservative (default)**: Use `bd` for task tracking. Do not run git commits, git pushes, or Dolt remote sync unless explicitly asked. At handoff, report changed files, validation, and suggested next commands.
- **Minimal**: Keep tool instruction files as pointers to `bd prime`; use the same conservative git policy unless active instructions say otherwise.
- **Team-maintainer**: Only when the repository explicitly opts in, agents may close beads, run quality gates, commit, and push as part of session close. A current "do not commit" or "do not push" instruction still wins.

## Session Completion

This protocol applies when ending a Beads implementation workflow. It is subordinate to explicit user, repository, and orchestrator instructions.

1. **File issues for remaining work** - Create beads for anything that needs follow-up
2. **Run quality gates** (if code changed) - Tests, linters, builds
3. **Update issue status** - Close finished work, update in-progress items
4. **Handle git/sync by active profile**:
   ```bash
   # Conservative/minimal/default: report status and proposed commands; wait for approval.
   git status

   # Team-maintainer opt-in only, unless current instructions forbid it:
   git pull --rebase
   git push
   git status
   ```
5. **Hand off** - Summarize changes, validation, issue status, and any blocked sync/commit/push step

**Critical rules:**
- Explicit user or orchestrator instructions override this Beads block.
- Do not commit or push without clear authority from the active profile or the current user request.
- If a required sync or push is blocked, stop and report the exact command and error.
<!-- END BEADS INTEGRATION -->

<!-- BEGIN BEADS CODEX SETUP: generated by bd setup codex -->
## Beads Issue Tracker

Use Beads (`bd`) for durable task tracking in repositories that include it. Use the `beads` skill at `.agents/skills/beads/SKILL.md` (project install) or `~/.agents/skills/beads/SKILL.md` (global install) for Beads workflow guidance, then use the `bd` CLI for issue operations.

### Quick Reference

```bash
bd ready                # Find available work
bd show <id>            # View issue details
bd update <id> --claim  # Claim work
bd close <id>           # Complete work
bd prime                # Refresh Beads context
```

### Rules

- Use `bd` for all task tracking; do not create markdown TODO lists.
- Run `bd prime` when Beads context is missing or stale. Codex 0.129.0+ can load Beads context automatically through native hooks; use `/hooks` to inspect or toggle them.
- Keep persistent project memory in Beads via `bd remember`; do not create ad hoc memory files.

**Architecture in one line:** issues live in a local Dolt DB; sync uses `refs/dolt/data` on your git remote; `.beads/issues.jsonl` is a passive export. See https://github.com/gastownhall/beads/blob/main/docs/core-concepts/sync-concepts.md for details and anti-patterns.
<!-- END BEADS CODEX SETUP -->
