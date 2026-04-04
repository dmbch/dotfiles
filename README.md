# dmbch's Dotfiles

Managed with [chezmoi](https://www.chezmoi.io/).

Modern CLI tools (`eza`, `bat`, `ripgrep`, `fd`, `sd`, etc.) replace their Unix ancestors. Language runtimes are pinned via [mise](https://mise.jdx.dev/). Ghostty, Zed, Zellij, and Starship handle terminal, editor, multiplexer, and prompt.

Claude Code ships with sandbox restrictions, a `/plan` → `/build` → `/review` workflow, and language-specific rules. The AI operates under the same git hygiene and testing standards as everything else. See [Security Model](#security-model) for what the sandbox allows and what it deliberately leaves open.

Three commands to bootstrap a new machine — Homebrew, mise, chezmoi — and you're done.

## Repository Structure

- `Brewfile`: Homebrew dependencies (CLI tools, Casks, and Mac App Store).
- `dot_zshrc`, `dot_zprofile`: ZSH configuration, aliases, and environment hooks.
- `dot_config/`:
  - `ghostty/`: GPU-accelerated terminal config.
  - `mise/`: Runtime management (Python, Node, Bun).
  - `starship.toml`: Prompt styling.
  - `zed/`: Editor settings.
  - `zellij/`: Terminal multiplexer config.
- `dot_claude/`: AI agent orchestration (agents, skills, rules).
- `.chezmoiignore`: Excludes documentation and repository metadata from deployment.

## Setup Sequence

0. **Setup**: grab dev tools `xcode-select --install`, and go home `cd ~`
1. **Homebrew**: Install [Homebrew](https://brew.sh) and run `brew install chezmoi mise`.
2. **Chezmoi**: Run `chezmoi init dmbch/dotfiles` and `chezmoi apply`.
3. **Mise**: Run `brew bundle` and `mise install` to set up language runtimes.

## Post-Install Setup

These require secrets or interactive auth that chezmoi can't manage.

1. **GPG**: Generate a key (`gpg --full-generate-key`) or import an existing one. Then configure pinentry and grab the key ID:
   ```
   echo "pinentry-program $(which pinentry-mac)" >> ~/.gnupg/gpg-agent.conf
   gpgconf --kill gpg-agent
   gpg --list-secret-keys --keyid-format long | grep sec | sed 's/.*\/\([^ ]*\).*/\1/'
   ```
2. **Git**: Set identity and signing with the key ID from above.
   ```
   git config --global user.name $GIT_HANDLE
   git config --global user.email $GIT_EMAIL
   git config --global user.signingkey $KEY_ID>
   git config --global commit.gpgsign true
   ```
3. **GitHub CLI**: Log in and upload the GPG public key so commits show as verified.
   ```
   gh auth login
   gpg --armor --export <KEY-ID> | gh gpg-key add
   ```
4. **Claude Code**: `claude` (follow the auth prompts)
5. **Gemini CLI**: `gemini` (follow the auth prompts)
6. **OrbStack**: `open -a OrbStack` (launch once to create `~/.orbstack/`)

## Security Model

Claude Code runs inside a sandbox (`dot_claude/settings.json`). Three layers of defense:

1. **Sandbox** constrains the blast radius. Filesystem writes are restricted to the current project, `~/src`, `~/.cache`, and `~/.claude`. Network is localhost-only (WebSearch and WebFetch go through Claude's infrastructure). Bash is auto-allowed within sandbox constraints.
2. **Deny list** blocks exfiltration and supply chain attacks. Secrets (`~/.aws`, `~/.gnupg`, `~/.ssh`, `~/.docker`, `.env`, `secrets/`, `*.key`, shell history) cannot be read. Global install commands (`brew install`, `mise install`, `pip install`, `npm install -g`, etc.) are denied. Destructive git operations and piping curl to a shell are blocked.
3. **Ask permissions** gate container commands. `docker`, `kubectl`, `helm`, `orb`, and `lazydocker` require explicit approval per invocation.
