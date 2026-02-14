# 06 jj Techniques and Aliases

Useful advanced patterns and aliases for faster day-to-day work.

## Reference links

- Docs: https://docs.jj-vcs.dev/latest/
- Config schema: https://jj-vcs.github.io/jj/latest/config-schema.json
- Types reference (templates): https://docs.jj-vcs.dev/latest/templates/#types

## Advanced config baseline

This is a cleaned, advanced baseline close to what you shared:

```toml
#:tombi schema.strict = true
#:schema https://jj-vcs.github.io/jj/latest/config-schema.json

[user]
name = "Swarnim Arun"
email = "swarnimarun11@gmail.com"

[ui]
default-command = "log"
diff-formatter = ":git"

[[--scope]]
--when.commands = ["diff", "show"]

[--scope.ui]
pager = "delta"

[git]
private-commits = "description('wip:*') | description('private:*')"

[templates]
git_push_bookmark = "\"swarnim/pr-\" ++ change_id.short()"

[remotes.origin]
auto-track-bookmarks = "*"

[remotes.upstream]
auto-track-bookmarks = "main | develop | master"

[signing]
behavior = "own"
backend = "gpg"
key = "1A0C34AE02141BA4"
```

## Why each section exists

- `[ui]`: fast defaults and a consistent diff experience.
- `[[--scope]]`: command-scoped overrides; here `delta` only for `diff/show`.
- `[git].private-commits`: protects local-only commits from accidental push.
- `[templates].git_push_bookmark`: deterministic PR-style bookmark naming.
- `[remotes.*].auto-track-bookmarks`: reduces manual tracking churn.
- `[signing]`: native commit signing in rewrite-heavy workflows.

## Techniques

## 1) Keep commits small on purpose

Work incrementally and commit often:

```bash
jj commit -m "feat: parse input"
jj commit -m "feat: validate schema"
```

Small commits make rebasing and review easier.

## 2) Use bookmarks as task handles

```bash
jj bookmark create feat-auth -r @
jj bookmark move feat-auth --to @
```

Treat bookmarks as movable pointers to the latest state of a task.

## 3) Rebase stacks instead of single commits

```bash
jj rebase -s feat-auth:: -d main@origin
```

`rev::` helps move the full descendant stack.

## 4) Fix commit shape before pushing

```bash
jj squash
jj split
jj rebase -s @ -d main
```

## 5) Use operation log as your safety net

```bash
jj op log
jj undo
jj redo
```

Do not panic-reset; recover deliberately.

## 6) Use `jj absorb` to auto-place hunks into ancestor commits

```bash
jj absorb
```

Great for cleaning up stacked review commits without manual interactive staging.

## 7) Prefer revsets in commands (not manual commit-id copying)

```bash
jj log -r "::@"
jj show -r "@-"
jj diff -r "main@origin..@"
```

## Alias setup (advanced)

Set aliases in user config:

```bash
jj config set --user aliases.s "status"
jj config set --user aliases.l "log"
jj config set --user aliases.d "diff"
jj config set --user aliases.ol "op log"
jj config set --user aliases.u "undo"
jj config set --user aliases.r "redo"
jj config set --user aliases.graph '["log", "--stat", "-r", "::@"]'
jj config set --user aliases.last '["log", "-r", "@", "--limit", "1"]'
jj config set --user aliases.what '["log", "-r", "::@", "--summary"]'
jj config set --user aliases.ft '["file", "track"]'
jj config set --user aliases.bc '["bookmark", "create"]'
jj config set --user aliases.bt '["bookmark", "track", "--remote", "origin"]'
jj config set --user aliases.recent '["bookmark", "list", "--stat", "--sort", "committer-date"]'
jj config set --user aliases.prune-all '["git", "fetch", "--prune"]'
jj config set --user aliases.aliases '["config", "list", "aliases"]'
```

Now these work:

```bash
jj s
jj l
jj d
jj ol
jj u
jj r
jj graph
jj recent
jj prune-all
```

## Alias map

- `graph`: compact working-stack graph with stats.
- `last`: inspect only current working commit.
- `what`: quick summary of everything up to `@`.
- `ft`: shortcut for `jj file track`.
- `bc`: create bookmark quickly.
- `bt`: track remote bookmark on `origin`.
- `recent`: see recently-updated bookmarks.
- `prune-all`: fetch + prune stale remote refs.
- `aliases`: print your alias table.

## Rich bookmark list alias (template-heavy)

This uses template expressions and is where the Types reference helps most.

```toml
[aliases]
bl = [
  "bookmark",
  "list",
  "--template",
  """
if(normal_target,
  label("bookmark",
    if(remote, name ++ "@" ++ remote, name)
  ) ++ " "
  ++ label("commit_id", normal_target.commit_id().short())
  ++ "\\n"
  ++ label("description", "> " ++ normal_target.description().first_line())
  ++ " "
  ++ label("stat",
       "(+" ++ normal_target.diff().stat().total_added()
       ++ "/-" ++ normal_target.diff().stat().total_removed()
       ++ ", " ++ normal_target.diff().stat().files().len() ++ " files)"
     )
  ++ "\\n"
  ++ normal_target.diff().files()
       .map(|f| label("file", "    - " ++ f.path()))
       .join("\\n")
  ++ "\\n\\n",
  label("conflict", name ++ " (no target)\\n\\n")
)
"""
]
```

## Revset aliases (high leverage)

```toml
[revset-aliases]
"mine()" = "author(\"swarnimarun11@gmail.com\")"
"wip()" = "description(\"wip:*\") | description(\"private:*\")"
"review_stack()" = "::@ & ~wip()"
```

Use:

```bash
jj log -r "mine()"
jj log -r "review_stack()"
```

## Notes and caveats

- Keep aliases short but obvious.
- Prefer aliases for frequent commands and introspection.
- Avoid opaque aliases for destructive operations.
- Revalidate config snippets after upgrading `jj`; command and config surface still evolves quickly.

## Community technique references

- Chris Krycho: https://v5.chriskrycho.com/journal/jj-init/
- Effective stacked-PR workflow examples: https://steveklabnik.github.io/jujutsu-tutorial/
