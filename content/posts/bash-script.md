---
title: "Bash script tips"
date: 2021-07-20T17:03:22+07:00
draft: false
---

For some small tasks, sometimes Bash script (shell script in general) is better than a full-blown program using programming languages. Here's some personal tips to write a Bash script:

## Exit on fail
Use `set -o errexit` to make the script exit when it fails. If some commands are allowed to fail, `command1 || echo "command1 is failed, but continuing"` can be used.

## Prevent access to undeclared variables
Use `set -o nounset` to exit when the script tries to use undeclared variables. Imagine running this command when undeclared variables is allowed:
```bash
rm -rf ${base_folder}/*
```

When the variable `$base_folder` is not set, it will become `rm -rf /*`.
If some variables are allowed to be undeclared, use `${variable_name:default_value}` to set `$variable_name` to `default_value` when it's not declared.

## Idempotency
What is idempotency?
> Idempotence is the property of certain operations in mathematics and computer science whereby they can be applied multiple times without changing the result beyond the initial application.

In short, it means that idempotent Bash scripts should have the same effects on the system even when called multiple times. Some example of idempotency in Bash script:

### Creating a symbolic link
The basic syntax of creating a symbolic link is:
```bash
ln -s source target
```

This is not idempotent, it will fail in the second run because the target is already exists. It can be solved by adding the `-f` flag:
```bash
ln -sf source target
```

If executed multiple times, it's still not idempotent because it will create a symbolic link inside the previously created symbolic link (nested). The idempotent way to create a symbolic link is:
```bash
ln -sfn source target
```

### Removing a file
The command `rm filename` will fail if the file is not exists, use `rm -f` instead. To remove folder, use `rm -rf`.

## Check if a file exists
Common usage is to download a file if only its not exist:
```bash
if [[ ! -f ${HOME}/.local/bin/random_binary ]]; then
  wget -qO ${HOME}/.local/bin/random_binary https://randomsite.com/random_binary
fi
```

## Use curly brace when using variable inside a string
When using variables inside a string, sometimes it's clearer to access the variable with the curly brace. So instead of this:
```bash
echo "$var1_new.yaml"
```

Prefer this:
```bash
echo "${var1}_new.yaml"
```

So it's clear that the variable name is `var1`, not `var1_new`.
