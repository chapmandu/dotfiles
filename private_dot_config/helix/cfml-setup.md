# CFML Tree-sitter Grammar for Helix

Adds syntax highlighting for `.cfc` (CFScript) and `.cfm` (CFML templates) using
[tree-sitter-cfml](https://github.com/cfmleditor/tree-sitter-cfml).

## 1. Add to `~/.config/helix/languages.toml`

```toml
[[language]]
name = "cfml"
scope = "source.cfml"
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
file-types = ["cfm", "cfml"]
block-comment-tokens = { start = "<!---", end = "--->" }
indent = { tab-width = 2, unit = "\t" }
roots = []

[[grammar]]
name = "cfhtml"
source = { git = "git@github.com:cfmleditor/tree-sitter-cfml.git", rev = "master", subpath = "cfhtml" }

[[language]]
name = "cfscript"
scope = "source.cfscript"
file-types = ["cfs"]
comment-tokens = "//"
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

```bash
mkdir -p ~/.config/helix/runtime/queries/{cfml,cfhtml,cfscript,cfquery}

for dialect in cfml cfhtml cfscript cfquery; do
  src="$HOME/.config/helix/runtime/grammars/sources/$dialect/$dialect/queries"
  dest="$HOME/.config/helix/runtime/queries/$dialect"
  for f in "$src"/*.scm; do
    ln -sf "$f" "$dest/"
  done
done
```

## 4. Patch upstream highlights.scm files

The grammar uses a Neovim-only predicate that Helix doesn't support. Replace it in
both `cfml` and `cfhtml` highlight queries:

```bash
for dialect in cfml cfhtml; do
  file="$HOME/.config/helix/runtime/grammars/sources/$dialect/$dialect/queries/highlights.scm"
  sed -i 's/(#lua-match? @comment.documentation "^\/\[\*\]\[\*\]\[^\*\].*\[\*\]\/$")/(#match? @comment.documentation "^\/\\*\\*[^*].*\\*\/$")/' "$file"
done
```

## Updating

When tree-sitter-cfml releases updates, fetch and rebuild, then re-apply the patch
(the fetch overwrites the modified highlights files):

```bash
hx --grammar fetch
hx --grammar build

for dialect in cfml cfhtml; do
  file="$HOME/.config/helix/runtime/grammars/sources/$dialect/$dialect/queries/highlights.scm"
  sed -i 's/(#lua-match? @comment.documentation "^\/\[\*\]\[\*\]\[^\*\].*\[\*\]\/$")/(#match? @comment.documentation "^\/\\*\\*[^*].*\\*\/$")/' "$file"
done
```

The query symlinks do not need to be recreated.

## Verify

```bash
hx --health languages | grep -E "cfml|cfhtml|cfscript"
```

Expect `✓` in the highlight column for all three.
