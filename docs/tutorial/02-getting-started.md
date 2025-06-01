---
id: getting-started
title: Installation
sidebar_label: Getting Started
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

Due to the fact that we have no stable release yet, you must install the project
through `cargo` or `nix`. We recommend installing through `cargo binstall` for fastest install times:

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

## Installing via Nix

For more advanced users, you can also use Nix Flakes to install Lux.

Simply add `github:nvim-neorocks/lux` to your `flake.nix` inputs, then add the `lux.packages.<target>.default` package.

For example:
```nix
{
  description = "Lux example flake";

  inputs = {
    nixpkgs.url = "github:nixos/nixpkgs?ref=nixpkgs-unstable";
    lux = {
      url = "github:nvim-neorocks/lux";
      inputs.nixpkgs.follows = "nixpkgs";
    };
  };

  outputs = { self, nixpkgs, lux, ... } @ inputs:
  let
    pkgs = nixpkgs.legacyPackages.x86_64-linux;
  in
  {
    devShells.x86_64-linux.default = pkgs.mkShell {
      name = "lux";
      buildInputs = [
        lux.packages.x86_64-linux.default
        pkgs.luajit # or pkgs.lua54Packages.lua
      ];
    };
  };
}
```

Now, you can run `nix develop` to enter a shell with Lux installed.

## Checking Installation

Ensure that installation was successful by running `lx help`.
You should see output similar to the following:
```
A fast and efficient Lua package manager

Usage: lx [OPTIONS] <COMMAND>

...
```

If you do, you're now ready to move on to the next section!
