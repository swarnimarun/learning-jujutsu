# 02 Operation Log, Restore, Undo/Redo

`jj` records every repo-changing action as an operation. This is the safety net.

This is awesome and the first thing most users should learn, so that you can undo your mistakes.

## Show operation history

```bash
jj op log
```

Use this when you need to find a point in time before a mistake.

## Inspect a specific operation

```bash
jj op show <operation-id>
```

## Undo the latest operation

```bash
jj undo
```

In modern jj, repeated `jj undo` steps backward sequentially through operation history.

## Redo what you just undid

```bash
jj redo
```

## Restore the whole repo to an older operation

```bash
jj op restore <operation-id>
```

This creates a new operation that makes your repo match that earlier state.

## Revert one specific historical operation

```bash
jj op revert <operation-id>
```

Useful when you want to undo one bad operation without rewinding everything in between.

## Practical recovery pattern

1. `jj op log`
2. Identify target operation
3. Preview state if needed with `jj --at-op <operation-id> log`
4. Use `jj undo`, `jj redo`, `jj op revert`, or `jj op restore`
