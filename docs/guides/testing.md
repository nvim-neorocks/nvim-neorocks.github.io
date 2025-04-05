---
id: testing
title: How to test a Lua project
---

In this guide, we'll learn how to set up a testing suite for a Lua project.

## Creating Spec Files

By default, Lux uses [`busted`](https://github.com/lunarmodules/busted) as its testing suite.
Tests are stored in a `spec/` directory in the root of your project, and only files
ending in `_spec.lua` are considered tests.

The type of tests you will make will vary greatly depending on the type of project at hand.
Nevertheless, refrain from making trivial tests - they bring little value to a codebase.

Below is an example of a test:
```lua title="spec/request_parse_spec.lua"
local request = require("mylib.request")
local json = require("mylib.json")

describe("request parsing", function()
    it("should successfully parse requests into json", function()
        local req = assert(request.get("https://example.com"))

        local j = json.from(req)

        assert.is_equal(j.response == 200)
    end)
end)
```

Notice how it tests the interactions between many aspects of the application.

Feel free to create any amount of tests you see fit before running the test suite.

## Running Tests

Running tests is done trivially by executing the command:

```sh
lx test
```

:::note
It may take a while to install `busted` for the first time.
:::

Upon completion, you should see a summary of all tests, optionally stating anything that went wrong:

```sh title="lx test"
●●
2 successes / 0 failures / 0 errors / 0 pending : 0.001615 seconds
```

## Customizing Tests

If you'd like to change the directory that Lux uses when searching for tests, or would like to customize the Lua interpreter
to be used, create a `.busted` file in the root of your project directory.

Below is an example of such a file:

```lua title=".busted"
return {
    _all = {
        -- Don't evaluate test coverage
        coverage = false,
        -- Tests should match this pattern
        pattern = "tests%.lua$",
        -- Use a custom Lua interpreter
        lua = "nlua",
        -- Search inside of the `lua/` directory
        ROOT = { "lua/" },
    },
}
```

For full reference, see [the `busted` website](https://lunarmodules.github.io/busted).
