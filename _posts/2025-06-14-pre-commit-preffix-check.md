---
layout: post
title: Pre-commit hook for filename consistency
date: 2025-06-14 07:00:01 +0100
categories: pre-commit ci/cd
---


Imagine you have a directory - maybe for migrations, test cases, or some other specific type of files - and you want to ensure every file inside follows a consistent naming pattern with a prefix like `migration_` or `testcase_`. I recently ran into this exact need.
Since our team use a [pre-commit](https://pre-commit.com/) in our daily workflows, I decided to create a small hook to enforce this rule automatically. It's lightweight, does one job well, and I've made it public in case it's useful to others too.
In this post, I'll walk you through what the tool does, how it works, and how to get started using it.

I created [check-filename-prefix](https://github.com/dunterov/check-filename-prefix) to solve that exact problem in one of my projects. It's a simple pre-commit hook that checks whether added or modified filenames start with a specific prefix. If not, it fails the commit with a clear message.

### First, what is pre-commit?

As it stated on their site - it's a framework for managing and maintaining multi-language pre-commit hooks.
Pre-commit hooks help catch simple issues (like formatting or debug code) before review, allowing code reviewers to focus on more meaningful things. As teams grew and projects multiplied, maintaining custom hooks became tedious and error-prone. To solve this, `pre-commit` was created - a multi-language package manager for pre-commit hooks. It simplifies sharing, installing, and running hooks across different projects and languages, without requiring root access or complex setup, even for tools outside your project's primary stack.

### Getting started with pre-commit and check-filename-prefix

To start make sure you have:

- pre-commit [installed](https://pre-commit.com/#install)
- a bash shell available
- a POSIX-compliant OS (Linux, macOS, WSL, etc.)

Once you there, create a file `.pre-commit-config.yaml` in a root of your project.
Then add the following content to your `.pre-commit-config.yaml`:

```yaml
- repo: https://github.com/dunterov/check-filename-prefix
  rev: v1.0.0
  hooks:
  - id: check-filename-prefix
    args:
    - "--prefix"
    - "my_prefix_"
```

Replace `my_prefix_` with your desired prefix. And basically this should be enough to try it.

### Try to run it

To run `pre-commit`, being in root directory (the directory with `.pre-commit-config.yaml` file) run the following command:

```shell
pre-commit run --all-files
```

As a result, you'll see something like this in the good case:

```shell
Ensure filenames contain the required prefix.............................Passed
```

And some variation of this in the case of failure:

```shell
Ensure filenames contain the required prefix.............................Failed
- hook id: check-filename-prefix
- exit code: 1

Invalid file name: "new_file.txt". File names must start with "my_prefix_".
```

Couple of words about exit codes (as pre-commit will display it).

- 0: All filenames are valid
- 1: At least one filename is invalid
- 2: Missing required --prefix argument

### Cool, but what if I need to only check files in a specific directory?

In this case, pre-commit config has special key - `files`. Here's an example: 

```yaml
- repo: https://github.com/dunterov/check-filename-prefix
  rev: v1.0.0
  hooks:
  - id: check-filename-prefix
    args:
    - "--prefix"
    - "my_prefix_"
    files: ^my_dir/
```

This configuration scopes pre-commit to files inside `my_dir` only.

### Right, but this directory already contains some file I want to exclude?

Also, not an issue. It's possible to use an `exclude` configuration key:

```yaml
- repo: https://github.com/dunterov/check-filename-prefix
  rev: v1.0.0
  hooks:
  - id: check-filename-prefix
    args:
    - "--prefix"
    - "my_prefix_"
    files: ^my_dir/
    exclude: ^my_dir2/myfile$
```

This configuration not only scopes pre-commit to files inside `my_dir`, but also configures pre-commit to exclude my_dir2/myfile from being processed by the hook.

You can combine those config keys or use them separatelly. Check the [official pre-commit documentation](https://pre-commit.com/#regular-expressions) for more details about regular expression suitable for exclude and files.

That's a wrap! Feel free to use this small tool in your projects - and if you'd like to suggest improvements or fix something, don't hesitate to open a pull request on the [repository](https://github.com/dunterov/check-filename-prefix).

Take care!