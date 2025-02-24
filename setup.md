# setting up jujutsu

## basic info and default command

Setup basic config at path, `jj config path --user`

```toml
[user]
name = "<Your-Name>"
email = "<Your-Email>"

[ui]
default-command = "log" # I recommend either st or log as default command
```

The log command is used to log the list of changes.
The st or status command is used to get the current changeset.

## fixing ssh to work correctly (requires atleast v0.26)

Often your ssh will just not work with jj this is cause jj uses libssh2 which doesn't load the `~/.ssh/config`.

To fix I recommend using git subprocess config so that local git cli is used.

```toml
[git]
subprocess = true
```

## gpg signing with your keys

Setup signing this is fairly easy to do, you can even copy paste your git config and change the field names.

```toml
[signing]
sign-all = true
backend = "gpg"
key = "<Key-you-want-to-use>"
```
