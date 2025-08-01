---
id: getting-started
title: Installation
sidebar_label: Getting Started
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

## Packages

Lux includes the following packages and libraries:

- `lux-cli`: The main CLI for interacting with projects and installing Lua packages
  from the command line.

- `lux-lua`: The Lux Lua API, which provides:
  - `lux.loader` for resolving potentially conflicting dependencies on `require` at runtime.
  - A work-in-progress API for embedding Lux into Lua applications.
  We provide builds of `lux-lua` for Lua 5.1, 5.2, 5.3, 5.4 and Luajit.

- `lux-lib`: The Lux library for Rust. A dependency of `lux-cli` and `lux-lua`.

## Installing Lux With a Distribution's Package Manager

### Arch User Repo (Arch Linux)

[![AUR package](https://repology.org/badge/version-for-repo/aur/lux-cli.svg)](https://aur.archlinux.org/packages/lux-cli)

lux-cli [is available in the AUR](https://aur.archlinux.org/packages/lux-cli).
You can install it with your favourite AUR helper.
It comes bundled with the `lux-lua` library, which Lux uses to resolve potentially
conflicting dependencies at runtime.

### Nix and NixOS

[![nixpkgs unstable package](https://repology.org/badge/version-for-repo/nix_unstable/lux-cli.svg)](https://search.nixos.org/packages?channel=unstable&show=lux-cli&from=0&size=50&sort=relevance&type=packages&query=lux-cli)

If you are using [Nix or NixOS](https://nixos.org/),
you can install [`lux-cli`](https://search.nixos.org/packages?channel=unstable&show=lux-cli&from=0&size=50&sort=relevance&type=packages&query=lux-cli)
and [`lux-lua` for various Lua versions](https://search.nixos.org/packages?channel=unstable&from=0&size=50&sort=relevance&type=packages&query=lux-lua).
You may also need to configure [`pkg-config`](https://search.nixos.org/packages?channel=unstable&show=pkg-config&from=0&size=50&sort=relevance&type=packages&query=pkg-config)
so that it can find `lux-lua` and Lua.

If you would like to use the latest release, our [source repository](https://github.com/nvim-neorocks/lux)
also provides a Nix flake.

## Installing The Latest Release Using Cargo

Due to the fact that we have no stable release yet, you must install the project
through `cargo` if you want the latest release and are not using the Nix flake.
We recommend installing through `cargo binstall` for fastest install times:

```sh
cargo binstall lux-cli --locked
```

If you don't have `cargo binstall` installed, you can install Lux normally with:

```sh
cargo install lux-cli --locked
```

:::important For new cargo users
Make sure that `~/.cargo/bin/` is part of your shell's `$PATH`!

<Tabs>
    <TabItem value="sh" value="bash / zsh">
    ```bash
    export PATH="$HOME/.cargo/bin:$PATH"
    ```
    </TabItem>
    <TabItem value="fish" value="fish">
    ```bash
    fish_add_path ~/.cargo/bin
    ```
    </TabItem>
    <TabItem value="powershell" value="powershell">
    ```powershell
    $env:Path = "$HOME\.cargo\bin;$env:Path"
    ```
    </TabItem>
</Tabs>
:::

## Checking Installation

Ensure that installation was successful by running `lx help`.
You should see output similar to the following:
```
A fast and efficient Lua package manager

Usage: lx [OPTIONS] <COMMAND>

...
```

If you do, you're now ready to move on to the next section!
