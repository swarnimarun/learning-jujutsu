# 04 My Workflow

A practical workflow for feature work against `origin/main`.

## One-time setup per repo

```bash
jj git fetch --remote origin
# generally tracked by default but just in case
jj bookmark track main --remote=origin
```

## Start feature work

```bash
# start work on top of main branch
jj new main@origin
# create a new feature branch
jj bookmark create feat-xyz -r @
```

Now code, then inspect:

```bash
jj status
jj diff
```

## Commit in small logical chunks

```bash
jj commit -m "feat: add xyz parser"
# continue coding
jj commit -m "feat: add xyz validation"
```

If you need cleanup:

```bash
jj squash

jj split

jj rebase -s @ -d main # rebase current changes on top of main
```

## Keep feature updated with remote main

```bash
jj git fetch --remote origin

# rebase the entire branch on top of updated main
jj rebase -s feat-xyz:: -d main@origin
```

## Push for review

```bash
jj bookmark move feat-xyz --to @
jj git push --bookmark feat-xyz
```

## Recovery when something goes wrong

```bash
jj op log
jj undo
# or
jj op restore <operation-id>
```

## Personal rules I follow

- Keep commits focused and reviewable.
- Rebase locally before pushing.
- Use bookmarks intentionally (`feat-*` naming).
- Use operation log instead of panic-reset habits.
