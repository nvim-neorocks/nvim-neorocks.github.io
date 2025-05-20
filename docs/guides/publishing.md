---
id: publishing
title: How to publish a Lua project
---

In this guide, we'll learn how to publish our Lua project to [luarocks.org](https://luarocks.org)
so it can be used by others.

## Host your Code

To publish your code to Luarocks, you first need to host your code on a forge such as
[Github](https://github.com/), [Gitlab](https://gitlab.com) or similar.

The forge you choose doesn't matter, however, for maximum compatibility, we recommend
using a Git forge as opposed to something like Mercurial or older VCSes.

Once you've properly set your remote and pushed your code, take note of the URL of your repository
and ensure that your repository's visibility is set to public.

## Publishing a Release

Apart from the code itself, you must also create a release for the code you published with a version number.

First, you must choose a version number. Lux requires that all projects follow
the [Semantic Versioning](https://semver.org/) specification. If your project is still unstable,
mark it as version `0.1.0`. If your code is ready for general use, mark it as
version `1.0.0`.
Lux can infer the version from a SemVer compliant git tag (with an optional `v` prefix).
In this case, you do not need to declare the version in your `lux.toml`.

There are many ways to keep track of versioning or releases. We recommend a
tool like [release-please](https://github.com/googleapis/release-please), which automatically
tracks a changelog and creates releases for you based on your commit messages.

If you do not want to use automation, most Git forges have UIs that allow you
to publish releases. Navigate to your forge and create a release named after
your version (e.g. `1.0.0`). If your forge doesn't have such functionality,
creating a git tag named after a version will also suffice.

## Configuring a Source URL

With the URL in hand, open your project's `lux.toml` and add the following section:

```toml title="lux.toml"
package = "my-lua-project"
version = "0.1.0"

# highlight-start
[source]
url = "https://github.com/my-username/my-project/archive/refs/tags/$(REF).zip"
dev = "git+https://github.com/my-username/my-project.git"

# highlight-end

...
```

`url` is a template for a SemVer release URL; in this case, a ZIP archive for the release tag.
Note the `$(REF)` variable in the URL template. Lux will substitute it with the tag or revision.
`dev` is a template for a dev release URL, which is a git URL in this example.

:::note
You could also specify `v$(VERSION)` to inject the version instead.
Additionally, you can inject the package name via `$(PACKAGE)`
or any environment variable via `$(VAR_NAME)`.
:::

:::important
We must provide the `git+https://` protocol in the `dev` URL template to instruct Lux to use `git`.
:::

## Acquiring an API Key

You can find your API key by logging into your account on
[luarocks.org](https://luarocks.org) and navigating to the [API Key
settings](https://luarocks.org/settings/api-keys).

From here, you can create a new API key or use an existing one.

### Configuring `direnv` (optional)

For serious projects, we highly recommend storing your API key in a `.env` file
and loading it with [`direnv`](https://direnv.net/) to prevent your API key
from being leaked through shell history.

Once you've configured `direnv`, set `LUX_API_KEY` to your key:

```plain title=".env"
LUX_API_KEY=your-key-here
```

## Publishing

With all the above prerequisites, we're now ready to publish.

If you've configured `direnv`, run:
```sh
lx upload
```

Otherwise, be sure to provide your API key as an exported environment variable:

```sh
LUX_API_KEY=your-key lx upload
```
