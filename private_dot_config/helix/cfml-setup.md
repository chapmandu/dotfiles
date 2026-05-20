# CFML Tree-sitter Grammar for Helix

Adds syntax highlighting for `.cfc` (CFScript) and `.cfm`/`.cfml` (CFML templates) using
[tree-sitter-cfml](https://github.com/cfmleditor/tree-sitter-cfml).

## 1. Add to `~/.config/helix/languages.toml`

```toml
[[language]]
name = "cfml"
scope = "source.cfml"
language-servers = [ "scls" ]
file-types = ["cfc"]
comment-tokens = "//"
block-comment-tokens = { start = "/*", end = "*/" }
indent = { tab-width = 2, unit = "\t" }
roots = []

[[grammar]]
name = "cfml"
source = { git = "git@github.com:cfmleditor/tree-sitter-cfml.git", rev = "master", subpath = "cfml" }

[[language]]
name = "cfhtml"
scope = "text.cfhtml"
grammar = "cfml"
language-servers = [ "scls" ]
file-types = ["cfm", "cfml"]
block-comment-tokens = { start = "<!---", end = "--->" }
indent = { tab-width = 2, unit = "\t" }
roots = []

[[language]]
name = "cfscript"
scope = "source.cfscript"
file-types = ["cfs"]
comment-token = "//"
indent = { tab-width = 2, unit = "\t" }
roots = []

[[grammar]]
name = "cfscript"
source = { git = "git@github.com:cfmleditor/tree-sitter-cfml.git", rev = "master", subpath = "cfscript" }

[[grammar]]
name = "cfquery"
source = { git = "git@github.com:cfmleditor/tree-sitter-cfml.git", rev = "master", subpath = "cfquery" }
```

## 2. Fetch and build

```bash
hx --grammar fetch
hx --grammar build
```

## 3. Link query files

Helix looks for highlight queries at `runtime/queries/<grammar-name>/`. The fetched
sources land at `runtime/grammars/sources/<grammar-name>/<subpath>/queries/`. Symlink
them so updates from a future fetch are picked up automatically.

```bash
mkdir -p ~/.config/helix/runtime/queries/{cfml,cfscript,cfquery}

for grammar in cfml cfscript cfquery; do
  src="$HOME/.config/helix/runtime/grammars/sources/$grammar/$grammar/queries"
  dest="$HOME/.config/helix/runtime/queries/$grammar"
  for f in "$src"/*.scm; do
    ln -sf "$f" "$dest/"
  done
done
```

`cfhtml` shares the `cfml` grammar and its queries — no separate step needed.

## Updating

When tree-sitter-cfml releases updates, fetch and rebuild:

```bash
hx --grammar fetch
hx --grammar build
```

The query symlinks do not need to be recreated.

## Verify

```bash
hx --health languages | grep -E "cfml|cfhtml|cfscript"
```

Expect `✓` in the highlight column for all three.
