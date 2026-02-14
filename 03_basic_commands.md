# 03 Basic Commands and Concepts

## Core ideas

- Working copy is represented as a real commit.
- Rewriting history is normal in jj.
- Bookmarks are jj's movable named pointers (similar role to Git branches).
- Operations are separate from commits and enable reliable recovery.

## Daily essentials

```bash
jj status            # workspace summary
jj log               # commit graph

jj diff              # working-copy changes

jj new               # create a new empty commit on top of <current> commit
jj new x             # create a new empty commit on top of <x> commit

jj describe -m "..." # set or edit commit message
jj commit -m "..."   # finalize current changes and create next working commit

jj edit <rev>        # make an existing revision current for editing

jj squash            # fold current change into parent
jj split             # split one commit into multiple commits

jj rebase -s <rev> -d <dest>  # move commits
```

## Bookmark basics

```bash
jj bookmark list

# -r @ sets the revision for which to create the bookmark, @ is the current revision, you can also use the revision id
jj bookmark create my-feature -r @

jj bookmark move my-feature --to @ # move the bookmark to the @ revision, allows us to change the revision for the branch

jj bookmark track my-feature --remote=origin
```

## Git remote sync

```bash
jj git fetch --remote origin
jj git push # push everything
jj git push --bookmark my-feature # push just a specific bookmark
```

## Avoid old commands

- Do not use `jj checkout` (removed).
- Do not use `jj merge` (removed).
- Do not use `jj init --git` (removed).

Use `jj new`, `jj edit`, and `jj git init` instead.
