# dmbch's Dotfiles

Managed with [chezmoi](https://www.chezmoi.io/).

Modern CLI tools (`eza`, `bat`, `ripgrep`, `fd`, `sd`, etc.) replace their Unix ancestors. Language runtimes are pinned via [mise](https://mise.jdx.dev/). Ghostty, Zed, Zellij, and Starship handle terminal, editor, multiplexer, and prompt.

Claude Code ships with sandbox restrictions, a `/plan` → `/build` → `/review` workflow, and language-specific rules. The AI operates under the same git hygiene and testing standards as everything else.

Three commands to bootstrap a new machine — Homebrew, mise, chezmoi — and you're done.

## Repository Structure

- `Brewfile`: Homebrew dependencies (CLI tools, Casks, and Mac App Store).
- `dot_zshrc`, `dot_zprofile`: ZSH configuration, aliases, and environment hooks.
- `dot_config/`:
  - `ghostty/`: GPU-accelerated terminal config.
  - `mise/`: Runtime management (Python, Node, Go).
  - `starship.toml`: Prompt styling.
  - `zed/`: Editor settings.
  - `zellij/`: Terminal multiplexer config.
- `dot_claude/`: AI agent orchestration (agents, skills, rules).
- `.chezmoiignore`: Excludes documentation and repository metadata from deployment.

## Setup Sequence

1. **Homebrew**: Install and run `brew install chezmoi mise`.
2. **Chezmoi**: Run `chezmoi init dmbch/dotfiles` and `chezmoi apply`.
3. **Mise**: Run `mise install` to set up language runtimes.
