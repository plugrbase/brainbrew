# Brainbrew

Compose targeted AI context files for Cursor, Copilot, Windsurf, Claude, and more — from modular markdown hosted on GitHub.

## How It Works

You maintain markdown modules in GitHub repos (coding conventions, stack guides, identity, etc.) and brainbrew composes them into the format each AI tool expects:

```bash
brainbrew compose --all
# ✓ Composed AGENTS.md (3 modules)
# ✓ Composed .github/copilot-instructions.md (3 modules)
# ✓ Composed .cursor/rules/brainbrew.mdc (3 modules)
# ✓ Composed .windsurfrules (3 modules)
# ✓ Composed CLAUDE.md (3 modules)
```

One source. Every tool.

## Install

```bash
git clone git@github.com:plugrbase/brainbrew.git ~/brainbrew
cd ~/brainbrew
./brainbrew link    # adds 'brainbrew' to /usr/local/bin
```

## Quick Start

```bash
cd ~/webroot/my-project
brainbrew init                 # Configure repo, sync, pick modules
brainbrew compose --all        # Generate context for all AI tools
```

`init` walks you through the setup interactively:

```
Include default BrainBrew modules? [Y/n]: Y
Additional GitHub repo (optional): your-user/ai-modules
Branch [main]:
Path (subfolder, optional):

▸ Fetching brainbrew/modules...
✓ Synced 8 file(s) from brainbrew/modules
▸ Fetching your-user/ai-modules...
✓ Synced 5 file(s) from your-user/ai-modules

  BrainBrew — Select modules
  ...
```

Say yes to default modules to get a curated set of general-purpose modules (coding conventions, git practices, etc.) from `brainbrew/modules`. Add your own repo on top for personal or team-specific modules.

## Commands

### init

```bash
brainbrew init    # Configure repo, sync modules, interactive picker
```

Creates `.brainbrew.yml` in the current project with the repo config and selected modules.

### update

```bash
brainbrew update                   # Interactive — offer defaults + ask for repo
brainbrew update --defaults        # Add default BrainBrew modules repo
brainbrew update user/ai-modules   # Add a specific repo
```

Add repos to an existing project. Useful if you skipped default modules during `init` or want to pull from additional sources later. Automatically syncs after adding.

### sync-repos

```bash
brainbrew sync-repos              # Fetch latest modules from configured repos
brainbrew sync-repos --dry-run    # Preview what would be fetched
```

### compose

```bash
brainbrew compose                    # AGENTS.md (default)
brainbrew compose --format copilot   # .github/copilot-instructions.md
brainbrew compose --format cursor    # .cursor/rules/brainbrew.mdc
brainbrew compose --format windsurf  # .windsurfrules
brainbrew compose --format claude    # CLAUDE.md
brainbrew compose --all              # All formats at once
```

### Other commands

```bash
brainbrew add <module>         # Add a module to current project
brainbrew remove <module>      # Remove a module
brainbrew list                 # List available modules
brainbrew status               # Show current project configuration
brainbrew global               # Symlink cursor/rules/*.mdc → ~/.cursor/rules/
```

## Project Configuration

Each initialized project gets a `.brainbrew.yml`:

```yaml
# Brainbrew — modules to compose into AGENTS.md
modules:
  - coding/conventions
  - coding/git
  - identity/me

repos:
  - repo: brainbrew/modules
    branch: main
  - repo: your-user/ai-modules
    branch: main
    path: modules    # optional: subfolder within the repo

# Project-specific rules (appended as last section)
project_rules: |
  - Always use UUIDs for public-facing IDs
  - Natural word capitalization
```

You can add multiple repos. The default `brainbrew/modules` repo provides general-purpose modules, while your own repos carry personal or team-specific ones. Use a **private repo** for personal modules (identity, preferences) and **public/org repos** for shared conventions.

Run `brainbrew compose` to regenerate after editing.

### Manual content

Brainbrew only manages its own section in each output file (between `<brainbrew>` / `</brainbrew>` markers in AGENTS.md, or `<!-- brainbrew:start -->` / `<!-- brainbrew:end -->` in other formats). Any content you add outside these markers is preserved when you re-run `compose`.

## Creating Modules

Create `.md` files in a GitHub repo. Any directory structure works:

```
ai-modules/
├── identity/
│   └── me.md
├── coding/
│   ├── conventions.md
│   └── laravel.md
└── stack/
    └── vue-inertia.md
```

After pushing, run `brainbrew sync-repos` to fetch the latest, then `brainbrew list` to browse.

## Tips

- Use `brainbrew compose --all` to keep all AI tools in sync at once.
- Add `.brainbrew.yml` and `AGENTS.md` to `.gitignore`, or version them to share context with collaborators.
- Run `brainbrew global` to install Cursor rules from `cursor/rules/` to `~/.cursor/rules/`.
- Export `GITHUB_TOKEN` for private repos.
