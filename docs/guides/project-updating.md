---
id: project-updating
title: How to publish a new release of your project
---

In this guide, we'll learn how to update our project to a new version and publish it to Luarocks.

## Prerequisites

This guide assumes that your code repository is using a version control system that is Git-compatible.
We also assume that you've already published a version of your project prior. If not, consult the
[How to publish a Lua project](/guides/publishing) guide.

## Publishing a new Release

Published packages on [luarocks.org](https://luarocks.org) should mirror the same version of your project.
Lux expects every project to adhere to [Semantic Versioning](https://semver.org).

If you're following up from [How to publish a Lua project](/guides/publishing)
and are using `release-please`, check your pull requests for an automatically
generated PR and merge it. This assumes you've been creating [Conventional
Commits](https://conventionalcommits.org), which `release-please` uses to gauge
when to create a pull request. Upon merging a new release tag will be created
for you automatically.

If you are creating your releases manually, consult your Git forge and see if there
is a "Releases" section from where you can create a new release. If not, create a git tag
named after your release, for example: `2.0.0`.

## Publishing

We're now ready to publish all of our changes to [luarocks.org](https://luarocks.org).
If you've configured `direnv`, run:

```sh
lx upload
```

Otherwise, be sure to provide your API key as an exported environment variable:

```sh
LUX_API_KEY=your-key lx upload
```
