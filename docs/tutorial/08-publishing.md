---
id: publishing
title: Publishing Our CLI Application
---

In this chapter, we'll learn how to publish our CLI application to [luarocks.org](https://luarocks.org)
so it can be used by others.

## API Key

To publish a rock, you need an API key. You can find your API key by logging into your account on
[luarocks.org](https://luarocks.org) and navigating to the [API Key settings](https://luarocks.org/settings/api-keys).

From here, you can create a new API key or use an existing one.
Here's an example of what the API key should look like:

```
wXnNFASnbacLCkgSzKb6t9cLSmNcO3ZV9mIaxrPK
```

:::warning Security
Keep your API key safe and secure. It's like a password and should not be shared with anyone.
The key we showed above is an example and not a real key.
:::

## Creating a Repository

As we mentioned in the previous chapters, a rockspec is a set of instructions
on how to build and install a rock. One of the fields in our `lux.toml`, which
isn't provided by default, is the URL source field, which points to the location
of the rock's source code.

When publishing a rock, we need to ensure that the `source.url` field points to
such a location, this is usually a GitHub repository.
If you haven't already made one, this is a good time to create a repository for your project.

Apart from creating the repository, you must also create a release for the code you published, along with a version number.
Navigate to your Github repository and press "Create new release" on the right sidebar. Afterwards, create a release named `v0.1.0`.

![Creating a release on GitHub](/img/creating-release.png)

Then press "Publish release". On your Github repository page, you should now see a release tagged `v0.1.0`!

## Adding a Source URL

Here's our updated `lux.toml` with the `source.url` field added:

```toml title="lux.toml"
package = "my-lua-project"
version = "0.1.0"
lua = ">=5.1"

[description]
summary = "A sample project."
maintainer = "vhyrro"
license = "MIT"
labels = [ "learning" ]

# highlight-start
[source]
url = "https://github.com/my-username/my-lua-project"
tag = "v0.1.0"
# highlight-end

[dependencies]
argparse = "=0.7"

[build]
type = "builtin"
```

## Publishing

With the API key in hand, we can now publish our rock. Run the following command in your project directory:

```sh
LUX_API_KEY=your-api-key lx upload
```

Lux will now package your project and upload it to luarocks!
Feel free to check out the [luarocks.org](https://luarocks.org) website to see your project listed in the "Recent Modules" section.

## Congratulations! :tada:

You have just successfully published your first Lua package! Well done.
If you'd like to learn more about Lux, feel free to take a look at our [guides](/guides).
