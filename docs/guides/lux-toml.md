---
id: lux-toml
title: How to declare a lux.toml file
---

In this guide, we'll learn about the `lux.toml` manifest format for Lux projects.

:::note
`lux.toml` is a subset of the [Lua rockspec format](https://github.com/luarocks/luarocks/blob/main/docs/rockspec_format.md),
with some simplifications, represented in TOML.
It does not support advanced specifications, like platform overrides.
You can still declare those with an [`extra.rockspec`](/guides/rockspec).
:::

## Package metadata

### `package`

The name of the rock (mandatory).

```toml title="example"
package = "my-lua-project"
```

:::note
Lux will always convert the package name to lower case,
for example when [publishing a rock](/guides/publishing).
:::

### `version`

The rock's version (optional).

```toml title="example"
version = "1.0.0"
```

Lux supports [SemVer](https://semver.org/) and "Dev" versions,
for compatibility with Luarock's dev manifest.

:::important
We strongly recommend sticking with SemVer and using pre-release
versions for development pre-releases.
:::

Lux is lenient when parsing SemVer versions. For example, `1.0` will be treated as `1.0.0`, 
as it can be corrected easily.
However, more complex versions that are not SemVer compliant
(for example ,`1.0-rc1` instead of `1.0.0-rc1`) will be treated as Dev versions, 
which cannot be compared with SemVer versions.

If the version is not set in the `lux.toml` and the project is in a git repository,
Lux will search the `HEAD` for a SemVer compliant git tag (with an optional `v` prefix).
If one is found, it will be used to parse the package version.
Otherwise, the version is assumed to be `dev`.

### `description`

Metadata that will be published to luarocks.org (optional).

```toml title="example"
[description]
summary = "A one-line description of the package"
detailed = """
A longer description of the package
that often spans multiple lines.
"""
license = "GPLv2" # SPDX license used by the package
homepage = "https://nvim-neorocks.github.io"
issues_url = "https://github.com/nvim-neorocks/lux/issues"
maintainer = "neorocks org"
labels = [ "neovim", "tls", "https" ]
```

## Source

To install a remote package, Lux needs to know where to find the source.
This may vary, depending on the package's version.
Unlike a Lua rockspec, Lux provides a mechanism for generating a [published](/guides/publishing)
rockspec's source.

You can declare templates for both SemVer and dev releases:

```toml title="example"
[source]
url = "https://host.com/owner/$(PACKAGE)/archive/refs/tags/$(REF).zip"
dev = "git+https://host.com/owner/$(PACKAGE).git"
```

Or:

```toml title="example"
[source]
url = "https://host.com/owner/$(PACKAGE)/archive/refs/tags/v$(VERSION).zip"
dev = "git+https://host.com/owner/$(PACKAGE).git"
```

When publishing a rock, Lux will perform the following variable substitutions:

- `$(PACKAGE)`: The package name.
- `$(VERSION)`: The package version 
                (generated from a git tag if not declared in the `lux.toml`).
- `$(REF)`: The git tag or revision
            (priority: 1. SemVer tags; 2. tags; 3. revision (commit SHA)).
- You may also specify environment variables with `$(<VAR_NAME>)`.

## Dependencies

Lux differentiates between five types of dependencies:

- `lua`: Used to declare which Lua versions your rock is compatible with.
- Regular `dependencies`: These are the runtime Lua dependencies of your rock.
- `build_dependencies`: Lua dependencies that are needed to build your rock.
  Typically, you will need to specify these if you are using a custom Luarocks build backend
  that is not implemented natively by Lux.
- `test_dependencies`: Lua dependencies needed to run `lx test`.
- `external_dependencies`: Compile time or FFI dependencies.
  Lux will default to using [`pkg-config`](https://www.freedesktop.org/wiki/Software/pkg-config/)
  to search for these, falling back to searching common directories.
  If an external dependency cannot be found, Lux will fail before attempting to compile the rock.

```toml title="example"
lua = ">=5.1" # Compatible with any Lua version greater than or equal to 5.1

[dependencies]
luafilesystem = "1.0.0"

[test_dependencies]
busted = ">=2.0.0"
nlua = "~>0.3"

[external_dependencies.OPENSSL]
header = "openssl/ssl.h"
library = "ssl"
```

Lux accepts the same operators as Luarocks: `==`, `~=`, `<`, `>`, `<=`, `>=` , and `~>`.
`~> 2` means `>= 2, < 3`; `~> 2.4` means `>= 2.4, < 2.5`, and so on.
The absence of an operator is the same as `==`.

Unlike the Luarocks rockspec format, the `lux.toml` format does not allow you to omit
the version requirement.

### Git dependencies

If a library your rock depends on isn't available on luarocks.org
and you don't want to wait for it to be published, you can specify git dependencies.

You can declare them as regular `dependencies`, `test_dependencies` or `build_dependencies`
with the `git` field:

```toml title="example"
[dependencies.luafilesystem]
version = "09511782201302ade916d4b250d01a6c61b56844" # The commit SHA or tag
git = "github:lunarmodules/luafilesystem"
```

Lux supports a full HTTPS or SSH git URL, or a shorthand with one of the following prefixes:

- `github:` (or none) - github.com
- `gitlab:` - gitlab.com
- `codeberg:` - codeberg.org
- `sourcehut:` - sourcehut.org

When building, Lux will search the repository's root directory for a `lux.toml` or
a `.rockspec` file, and will use its `build` specification to build the rock.
If there is no `lux.toml` or `.rockspec`, Lux will default to the `builtin` build.

:::important
- To ensure rockspecs can be built by luarocks, Lux will refuse to upload rocks
  that have git dependencies.
- Currently, Lux does not support git dependencies that require a custom luarocks
  build backend.
:::

Alternately, you can specify a `rev` field, if you would like to set the version to
something else:

```toml title="example"
[dependencies.luafilesystem]
version = "scm"
git = "github:lunarmodules/luafilesystem"
rev = "09511782201302ade916d4b250d01a6c61b56844" # The commit SHA or tag
```

If the `rev` is not set, Lux will treat the `version` field as the commit SHA or tag
to check out.

## Build specification

You can use the `build` table to let Lux and Luarocks know how to build your rock.
Typically, you will want to do so if your rock has modules that are written in another
language, like C or Rust.

In your `lux.toml`, you can specify build rules for the following build backends.

:::important
Rocks that are (partially) written in C need a C compiler to build.
Lux does not bundle one for you. You must install it separately.
:::

### Build backends

The build backend tells Lux how to build a package.

#### `builtin` (default)

If you don't specify a build backend, Lux will default to the `builtin` build backend,
which automatically detects Lua and C sources. C sources will be compiled to a Lua module.
If you have more complex C modules, you can use the optional `modules` table to tell
Lux how to build them.

```toml title="example"
[build]
type = "builtin"

[build.modules]
"foo.main" = "lua/foo.lua" # You can rename Lua sources
# And you can combine multiple C files into a single Lua module
"foo.bla" = [ "src/bla.c" ] # A module can be a list of C sources...

# ...or a table that tells Lux how to find dependencies
[build.modules."foo.core"]
sources = [ "src/core.c", "src/extra.c" ] # relative C source paths (mandatory)
libraries = [ "ssl" ] # External libraries to be linked (optional)
defines = [ "FOO=bar", "USE_BLA" ] # C defines (optional)
incdirs = [ ] # Directories to be added to the headers lookup directory list (optional)
libdirs = [ ] # Directories to be added to the linker's library lookup directory list (optional)
```

####  `make`

With the `make` build backend, Lux will delegate to Make to build your rock.

```toml title="example"
[build]
type = "make"

# The following fields are optional.
# This example shows the defaults
makefile = "Makefile"
build_target = ""
build_pass = true # Whether to perform a make pass on the build_target
install_target = "install"
install_pass = true # Whether to perform a make pass on the install_target
build_variables = {} # (table) Assignments to be passed to make during the build pass
install_variables = {} # (table) Assignments to be passed to make during the install pass 
variables = {} # (table) Assignments to be passed to make during both build and install passes
```

:::important
To be able to build rocks that use this build backend, Make must be installed.
:::

#### `cmake`

With the `cmake` build backend, Lux will delegate to CMake to build your rock.

```toml title="example"
[build]
type = "cmake"

# The following fields are optional.

# CMakeLists.txt content. If not set, Lux will search for a CMakeLists.txt file.
cmake = """
<content>
"""

# Additional variables to be passed to CMake, equivalent to -Dkey1=value1 -Dkey2=value2 arguments
variables = []
```

When building a rock with the `cmake` build backend, Lux will also use the following
environment variables:

- `CMAKE_MODULE_PATH`: Where to search for additional modules.
- `CMAKE_INCLUDE_PATH`: Path to the include directory.
- `CMAKE_LIBRARY_PATH`: Path to libraries.

:::important
To be able to build rocks that use this build backend, CMake must be installed.
:::

#### `command`

If you want to use simple commands to build and install your rock, you can use
the `command` build backend.

```toml title="example"
[build]
type = "command"

# The following fields are optional.
build_command = ""
install_command = ""
```

#### `none`

If you only need to copy source files without any build steps, you can
set the build backend to `none`.

```toml title="example"
[build]
type = "none"
```

#### `rust-mlua`

Lux has native support for building rocks that are written in Rust and compiled
to Lua using the [`mlua`](https://crates.io/crates/mlua) crate.

```toml title="example"
[build]
type = "rust-mlua"

# The following fields are optional.
# This example shows the defaults
target_path = "target"
default_features = false # Passes --no-default-features to Cargo.
features = [] # Lux will automatically pass the correct mlua feature based on the Lua version.


[build.modules] # (optional)
# This expects that Cargo will build a native library in `<target>/release/libalt_name.so`.
"foo.main" = "alt_name"

[build.include] # (optional)
# Copy additional files to the Lua directory
"relative/path/to/file.lua" = "target-file.lua"
```

:::important
To be able to build rocks that use this build backend, you need a [Rust toolchain](https://doc.rust-lang.org/cargo/getting-started/installation.html).
:::

#### `treesitter-parser`

Many tree-sitter parsers have been published as Luarocks packages.
Unlike luarocks, Lux does not require you to have tree-sitter CLI installed,
as it uses the tree-sitter Rust libraries.


```toml title="example"
[build]
type = "treesitter-parser"

# (required) Name of the parser language (e.g. "lua").
lang = "<lang>" 

# (optional) Will only install queries without building a parser if `false`.
parser = true

# (optional) Do the sources need to be generated?
generate = true

# (optional) Generate sources from src/grammar.json?
generate_from_json = false

# (optional) tree-sitter grammar's location (relative to the source root)
location = "libs/tree-sitter-<lang>"

# Queries to install. Overwrites any existing queries.
# This example installs a `queries/<lang>/highlights.scm` file.
[build.queries."highlights.scm"] = """
(signature
  name: (variable) @function)

(function
  name: (variable) @function)
"""
```

#### Custom Luarocks build backends

[Luarocks](https://github.com/luarocks/luarocks/) supports the use of custom build
backends, which are written in Lua.
To be able to fully support these, Lux would have to implement Lua bindings for every
single public Lua module that Luarocks exposes. This is not feasible.
Instead, Lux installs Luarocks (which is just another rock) and any `build_dependencies`,
as well as the custom build backend into a special build directory.
Then, it shells out to luarocks for the build process and translates the resulting
luarocks build into a Lux rock installation.

### Install specification

Some packages don't provide a means to install modules.
With `[build.install.*]`, you can specify categories of files to install.

```toml title="example"
# Lua modules written in lua
[build.install.lua]
"foo.bar" = "src/bar.lua" # Installs `src/bar.lua` as a `foo.bar` module

# Compiled Lua modules
[build.install.lib]
"foo.bar" = "lib/bar.so"

# Configuration files
[build.install.conf]

# Scripts to be installed into the install tree's `bin` directory.
[build.install.bin]
```

### `copy_directories`

Directories in the source directory to be installed to the rock's `etc` tree as-is.
This is useful for rocks such as plugins that use additional runtime files.

```toml title="example"
# Lua modules written in lua
[build]
copy_directories = [ "plugin", "ftplugin" ]
```

:::important
Do not use the following directory names: "lua", "lib" or "rock_manifest".
They have a special meaning for Luarocks and specifying them will cause a clash
when building your published rock with Luarocks.
:::

### Patches

If you are packaging a rock that you don't maintain yourself, you may need to patch it.
You can do so by embedding patch files:

```toml title="example"
[build.patches]
"lua51-support.diff" = """
--- old/mymodule.c....
"""
```

:::important
Lux supports any patch format supported by the [diffy crate](https://docs.rs/diffy/0.4.2/diffy/).
Luarocks, on the other hand, uses a hand-rolled Lua implementation for
applying patches in the unified diff (`diff -u`) format.
It is quite limited. For instance, luarocks cannot apply patches that create new files.
If you want your rock to be compatible with luarocks, you should test an installation with luarocks
before publishing.
:::

<!-- ## Test specification -->
<!-- TODO -->
