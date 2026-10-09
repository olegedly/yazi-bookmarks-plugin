# yazi-bookmarks-plugin

A compatibility fork of [dedukun/bookmarks.yazi](https://github.com/dedukun/bookmarks.yazi),
packaged in yazi's monorepo layout.

Upstream is no longer maintained — its `main` last moved on 2025-07-09 — and it
still calls `ya.mgr_emit()`. That function was deprecated in favour of
`ya.emit()` and has since been removed from yazi, so on yazi 26.9.1 jumping to a
bookmark fails with:

```
attempt to call a nil value (field 'mgr_emit')
```

This fork carries a two-line fix for that. It is otherwise unchanged from
upstream.

## Layout

```
bookmarks.yazi/   the plugin itself (deployed to ~/.config/yazi/plugins/bookmarks.yazi)
```

The repository is deliberately *not* named `bookmarks.yazi` so that it is not
mistaken for the upstream project; the plugin directory inside it keeps the name
yazi expects.

## Install

```sh
ya pkg add olegedly/yazi-bookmarks-plugin:bookmarks
```

The `:bookmarks` suffix selects the plugin inside this repository.
