# 01 Setup

## Install

Install `jj` from the official release binaries or package manager:

- Docs: https://docs.jj-vcs.dev/latest/install-and-setup/
- Releases: https://github.com/jj-vcs/jj/releases

## Configure identity

```bash
jj config set --user user.name "Your Name"
jj config set --user user.email "you@example.com"
```

## Start a repo

Create a new Git-backed repo:

```bash
jj git init my-repo
cd my-repo
```

Or clone an existing Git remote:

```bash
jj git clone https://github.com/<org>/<repo>.git
cd <repo>
```

## Verify install and repo state

```bash
jj --version
jj status
jj log
```

## Notes for modern jj

- `jj git init` replaces old `jj init --git`.
- Colocated Git repos are the default in modern jj releases.
- `jj` has an operation log, so most mistakes are recoverable (`jj undo`, `jj op log`, `jj op restore`).
