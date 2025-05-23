---
id: testing
title: Testing Our Code
---

Testing allows us to ensure that our code behaves as expected and that we don't
introduce regressions when making changes. In this chapter, we'll learn how to
write tests for our Lua code.

:::info Testing Framework
In Lua, testing is primarily done using the
[`busted`](https://github.com/lunarmodules/busted) testing framework.
Lux takes care of installing `busted` for you when running tests!
:::

## Restructuring Our Project

To start testing our code, we need to restructure our project a little. First, let's
move out our newly made `has_hello` function into its own file. Create a new file
called `src/has_hello.lua` and move the function there:

```lua title="src/has_hello.lua"
--- Checks whether input has the word "hello"
---@param input string
local function has_hello(input)
    return input:lower():find("hello") ~= nil
end

return {
    has_hello = has_hello,
}
```

Next, let's update our `main.lua` file to use this new file:

```lua title="src/main.lua"
-- highlight-next-line
local has_hello = require("has_hello").has_hello
local argparse = require("argparse")

-- Create an argument parser
local parser = argparse("script", "An example.")
parser:argument("input", "Input file.")

local args = parser:parse()

-- Directly print out the "input" argument
if has_hello(args.input) then
    print("Your input has the word 'hello' in it!")
else
    print("Your input does not have the word 'hello' in it!")
end
```

Noticed how we replaced the function with a `require` call to the new file.

## Writing our First Test

Now that we have our code structured, let's write our first test. Tests are usually stored in
a `spec/` directory, and each test file must end in `_spec.lua`. Create a new
directory called `spec/` in the root of your project:

```sh
mkdir spec
```

Inside the `spec/` directory, create a new file called `has_hello_spec.lua`:

```lua title="spec/has_hello_spec.lua"
local has_hello = require("has_hello").has_hello

describe("has_hello", function()
    it("should return true if input has the word 'hello'", function()
        assert.is_true(has_hello("hello world"))
    end)

    it("should return false if input does not have the word 'hello'", function()
        assert.is_false(has_hello("world"))
    end)
end)
```

## Running the Tests

We're now ready to run our tests! `lx test` will run all the tests in your
project. After taking a moment to install `busted`, Lux should show you the following output:

```sh title="lx test"
●●
2 successes / 0 failures / 0 errors / 0 pending : 0.001615 seconds
```

If so, congratulations! You've successfully written and ran your first test!
