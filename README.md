# yazi-bookmarks-plugin

A compatibility fork of [dedukun/bookmarks.yazi](https://github.com/dedukun/bookmarks.yazi).

Upstream is no longer maintained — its `main` last moved on 2025-07-09 — and it
still calls `ya.mgr_emit()`. That function was deprecated in favour of
`ya.emit()` and has since been removed from yazi, so on yazi 26.9.1 jumping to a
bookmark fails with:

```
attempt to call a nil value (field 'mgr_emit')
```

## Differences from upstream

Based on upstream `main` at
[`9ef1254`](https://github.com/dedukun/bookmarks.yazi/commit/9ef1254). There are
exactly two.

### 1. `ya.mgr_emit()` → `ya.emit()` in `bookmarks.yazi/main.lua`

The reason this fork exists. Both emit calls are switched to the replacement API:

```diff
--- a/main.lua
+++ b/bookmarks.yazi/main.lua
@@ -310,9 +310,9 @@ return {
 			if bookmarks[selected].is_parent then
-				ya.mgr_emit("cd", { bookmarks[selected].path })
+				ya.emit("cd", { bookmarks[selected].path })
 			else
-				ya.mgr_emit("reveal", { bookmarks[selected].path })
+				ya.emit("reveal", { bookmarks[selected].path })
 			end
 		elseif action == "delete" then
```

`ya.mgr_emit` was the only call in the plugin that no longer exists in yazi
26.9.1 — the rest (`ya.input`, `ya.notify`, `ya.sync`, `ya.which`) are all still
current.

### 2. The plugin lives in a `bookmarks.yazi/` subdirectory

Upstream keeps `main.lua`, `README.md` and `LICENSE` at the repository root.

This fork keeps those same files under `bookmarks.yazi/`, so that the repository
can carry a descriptive name. For a bare `use = "owner/repo"`, `ya pkg` appends
`.yazi` to the repository name *and* names the installed plugin directory after
it — a repository called `yazi-bookmarks-plugin` would therefore need to be named
`yazi-bookmarks-plugin.yazi` and would install to
`~/.config/yazi/plugins/yazi-bookmarks-plugin.yazi`, which breaks
`require("bookmarks")` in `init.lua` and the `plugin bookmarks …` keymaps.

The `owner/repo:child` form used below avoids that: the repository keeps its
descriptive name, the plugin inside is still called `bookmarks`, and nothing
downstream has to change.

### Unchanged

`bookmarks.yazi/README.md`, `bookmarks.yazi/LICENSE` and `stylua.toml` are
byte-identical to upstream. `bookmarks.yazi/main.lua` differs only by the two
lines shown above.

## Commits on top of upstream

```
$ git log --oneline upstream/main..main
5c3eb7a chore: move the plugin into a `bookmarks.yazi/` subdirectory
4ebd02b fix: use `ya.emit` instead of removed `ya.mgr_emit`
```

## Install

```sh
ya pkg add olegedly/yazi-bookmarks-plugin:bookmarks
```

The `:bookmarks` suffix selects the plugin inside this repository. Configuration
through `init.lua` is unchanged from upstream:

```lua
require("bookmarks"):setup({ ... })
```

## Keeping in sync with upstream

```sh
git remote add upstream https://github.com/dedukun/bookmarks.yazi.git
git fetch upstream
git log --oneline upstream/main..main   # what this fork adds
```
