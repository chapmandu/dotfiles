# ESLint in Helix

This project uses ESLint 9 with flat config (`eslint.config.mjs`). As of writing, `vscode-langservers-extracted` 4.10.0 (latest) has a known compatibility issue with ESLint 9 flat config that prevents inline diagnostics from appearing in Helix.

The fix is to pin `vscode-langservers-extracted` at 4.8.0.

## Installation

Brew doesn't have a versioned formula for this package, so install via a local tap using the homebrew-core commit for 4.8.0:

```bash
brew tap homebrew/core --force
brew version-install vscode-langservers-extracted 4.8.0
brew pin vscode-langservers-extracted@4.8.0
```

## Helix config

Add the following to `~/.config/helix/languages.toml`:

```toml
[language-server.eslint]
command = "vscode-eslint-language-server"
args = ["--stdio"]

[language-server.eslint.config]
validate = "on"
run = "onType"
experimental = { useFlatConfig = true }

[[language]]
name = "typescript"
language-servers = ["typescript-language-server", "eslint"]
```

## Upgrading later

Once `vscode-langservers-extracted` adds proper ESLint 9 support:

```bash
brew unpin local/custom/vscode-langservers-extracted
brew untap local/custom
brew install vscode-langservers-extracted
```
