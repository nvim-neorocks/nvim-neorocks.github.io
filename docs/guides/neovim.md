---
id: neovim
title: How to use Neovim with Lux
---

In this guide, we'll learn how to make Lux execute code using Neovim as the Lua interpreter.

## Ensuring Lua Version

Neovim ships with a `luajit` interpreter, which is equivalent to Lua version `5.1`.
In your `lux.toml`, ensure that you specify version `5.1`:

```toml title="lux.toml"
package = "package-name"
version = "0.1.0"
# highlight-next-line
lua = "==5.1"

...
```

## Customizing `run`

Neovim allows you to run scripts using `nvim -l`. To accommodate for this in Lux, edit your `lux.toml`
and modify the `[run]` section as follows:

```diff title="lux.toml"
[run]
-args = [ "src/main.lua" ]
+command = "nvim"
+args = [ "-l", "src/main.lua" ]
```

## Running our Code

Neovim is now hooked up to Lux. Any calls to `lx run` will now execute through Neovim, allowing you to use Neovim's
Lua API.
