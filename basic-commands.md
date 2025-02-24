# basic jj commands

## git clone

You can just git clone directly,

```bash
jj git clone <url>
```

## commits/changes and revsets

> Note: In `jj` all changes are labelled and called a changeset which can be converted to commits.

Update the description of the current changes, and then create a new changeset.

Essentially this takes the current tracked changes makes a commit and then creates a new changeset.

```bash
jj commit
```

## untrack

All changes are tracked by default you can untrack specific files manually though which is awesome.

```bash
jj file untrack "<path>"

jj file track "<path>"
```

## bookmark(s)

Bookmarks are like branches for jj.

```bash
jj bookmark
```

## git pull ??

You generally don't need git pull.

## git push

Push will create a new target branch if you use the flag `--allow-new`.

```bash
jj git push
```

## status

Get the status of the changes for the project.

```bash
jj st
```

## logs

jj provides quite a lot of types of logs. Which provide quite a lot of insight into your vcs.

```bash
jj log

jj evolog # log about the evolution of code

jj op log # these are related to op log
```

## workspace

TODO

## restore/resolve/conflicts/fix

TODO
