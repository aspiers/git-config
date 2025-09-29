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
