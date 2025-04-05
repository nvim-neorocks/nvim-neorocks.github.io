---
id: code-hygiene
title: Code Hygiene
---

Code hygiene is the practice of writing code that is readable and maintainable.
In this chapter, we'll let tools automatically clean up our code for us.

## Linting with `lx check`

Linting is the process of analyzing code for potential errors and stylistic issues (lines too long, unused variables, etc.).
To lint the entire project, we can run:

```sh
lx check
```

<!--TODO: show output of lx check and fix the errors that exist there-->

## Formatting with `lx fmt`

Lux comes with a Lua code formatter that can automatically format your Lua code to a consistent style.
To make our code style consistent accross the codebase, we can run:

```sh
lx fmt
```

Feel free to take a look at the changes `lx fmt` made to our code!

Now that our code is nice and shiny, we're ready to move onto the final step:
publishing our code to [luarocks.org](https://luarocks.org)!
