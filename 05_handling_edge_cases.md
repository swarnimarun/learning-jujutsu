# 05 Handling Edge Cases

Practical recovery patterns for common "oh no" moments in `jj`, including edge cases seen by early adopters.

## 1) You rewrote history and want to go back

```bash
jj op log
jj undo
```

If needed, restore to a specific operation:

```bash
jj op restore <operation-id>
```

## 2) You edited the wrong commit

```bash
jj edit <correct-rev>
jj squash
# or
jj split
```

## 3) You committed with a bad description

```bash
jj describe -m "better message"
# or target another revision
jj describe -r <rev> -m "better message"
```

## 4) You included unrelated files

```bash
jj split
jj restore <path>
```

## 5) Rebase created conflicts

In `jj`, rebases can complete while leaving conflict markers in content. Resolve and re-check:

```bash
jj resolve
jj diff
jj log
```

If you rebased the wrong stack:

```bash
jj undo
```

## 6) Push rejected because remote moved

```bash
jj git fetch --remote origin
jj rebase -s <your-bookmark>:: -d <target>@origin
jj git push --bookmark <your-bookmark>
```

## 7) Conflicted bookmark target after fetch/import

Inspect bookmark state:

```bash
jj bookmark list
```

Pick the intended target and move it explicitly:

```bash
jj bookmark move <name> --to <rev>
jj git push --bookmark <name>
```

## 8) Immutable commit protection blocks rewrite

You may see an error when trying to rewrite protected history (for example trunk or tags).

Check immutable heads policy in config:

```toml
[revset-aliases]
"immutable_heads()" = "builtin_immutable_heads()"
```

Workaround: rebase your feature stack away from immutable commits instead of rewriting them.

## 9) Private/WIP commits accidentally selected for push

Set a guardrail:

```toml
[git]
private-commits = "description('wip:*') | description('private:*')"
```

Then mark local-only commits consistently, for example `wip: ...`.

## 10) SSH works in Git but fails in jj

A known rough edge in some setups is libssh behavior and key loading.

Practical mitigations:

1. Ensure keys are loaded in `ssh-agent`.
2. Configure `jj` to use Git subprocess transport when needed:

```toml
[git]
subprocess = true
```

## 11) Tooling breaks when `.git` is hidden (non-colocated mode)

Some tools expect a normal `.git` directory.

Modern `jj` provides colocation controls:

```bash
jj git colocation enable
# or
jj git colocation disable
```

Use colocated mode if external tooling depends heavily on Git internals.

## 12) Config location confusion after upgrades

As of `jj` 0.38.0 (2026-02-04), repo/workspace config storage changed and legacy `.jj/repo/config.toml` is no longer the long-term location.

Use `jj config` subcommands to inspect/update instead of manually editing paths:

```bash
jj config list
jj config edit --user
jj config edit --repo
jj config edit --workspace
```

## 13) Revset expression used to work, now errors on multi-match

Recent versions tightened symbol resolution in revsets. If a symbol could resolve to multiple revisions, use explicit selectors:

```bash
jj log -r "change_id(<prefix>)"
jj log -r "bookmarks(exact:<name>)"
```

## 14) Your stack diverged too far; you are not sure what changed

```bash
jj log -r "::@"
jj op log
jj diff -r @-
```

Compare repository state at a historical operation:

```bash
jj --at-op <operation-id> log
```

## 15) Safe default recovery loop

1. `jj op log`
2. Identify candidate operation IDs
3. Try `jj undo` first
4. Prefer `jj op revert <op-id>` for targeted rollback
5. Use `jj op restore <op-id>` only for full-state restoration

## Community notes that inspired these cases

- Arne Bahlo: https://arne.me/blog/jj-in-practice/
- Tony Finn: https://tonyfinn.com/blog/jj-vcs/
- ThePrimeagen team writeup: https://www.josean.com/posts/jj-fix-bad-git-process
