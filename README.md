# Guild

Guild is the shared roster of Claude Code subagents that Relay and Gauntlet dispatch. Keeping the agents in one repository lets those projects be audited together with the roles they depend on.

## How the roles fit together

1. `architect` writes a design under `docs/superpowers/specs/` and, on a `rulings` pass, records the owner's rulings and approval.
2. `divvyer` cuts the approved design into the queue a build runs from, and re-cuts a section or step after a stop.
3. `researcher` maps one queue section's code before its lead starts, fanning the files out to `reader` agents in parallel and stitching their pointers into one map.
4. `lead` runs the section in its own worktree: `test-writer` writes the red tests, `builder` or a specialist (`core-builder`, `seamstress`, `gatesmith`) makes them pass, `diff-reviewer` and `test-reviewer` review each diff blind, and `mutant-hunter` kills surviving mutants.
5. `spiker` answers a measurement question by experiment, `juror` judges a Foreman check that cannot reach green, and `role-author` writes the role prompts in this repository.

## Agents

| Agent | Role | Tools | Model | Effort |
|---|---|---|---|---|
| `architect` | Writes one design file per dispatch, or records rulings and approval on one; never builds. | read, write, Bash | opus | medium |
| `divvyer` | Cuts an approved design into the build queue, and re-cuts it after a stop. | read, write, Bash | opus | medium |
| `researcher` | Relay: maps one queue section's code before its lead starts, as `path:line` pointers; answers follow-ups from `relay ask`. | read, Bash, dispatches `reader` only | (inherits) | medium |
| `reader` | Reads one group of files for `researcher` and returns at most 40 lines of `path:line` pointers. | read-only | opus | medium |
| `lead` | Runs one workstream in its own worktree: dispatches workers, re-runs done-checks, commits; also rules on a stopped Foreman group. | read, Bash, dispatches agents, Terminus | opus | (not set) |
| `builder` | Builds one Order step of an approved design within the paths it lists. | read, write, Bash, Terminus | sonnet | medium |
| `core-builder` | Gauntlet: builds one step in the pure layer, `gauntlet/core/`. | read, write, Bash, Terminus | sonnet | medium |
| `seamstress` | Gauntlet: builds one step in the thin I/O layer, `gauntlet/seams/`. | read, write, Bash, Terminus | sonnet | medium |
| `gatesmith` | Gauntlet: builds gates, build configuration and the pre-commit hook. | read, write, Bash, Terminus | sonnet | medium |
| `test-writer` | Writes the repository's test files, one step per dispatch. | read, write, Bash, Terminus | opus | medium |
| `mutant-hunter` | Kills one surviving mutmut mutant with a new test, or rules it equivalent. | read, write, Bash, Terminus | sonnet | medium |
| `diff-reviewer` | Blind reviewer of a diff's code, build files and test helpers. | read, Bash | opus | medium |
| `test-reviewer` | Blind reviewer of a diff's test files. | read, Bash | opus | medium |
| `juror` | Foreman: read-only judge of a check that cannot reach green. | read-only | opus | medium |
| `spiker` | Answers one question with a throwaway experiment in the scratchpad. | read, write, Bash, Terminus | sonnet | medium |
| `role-author` | Writes one agent role prompt per dispatch. | read, write, Bash, Terminus | opus | medium |

"read" is Read, Grep and Glob; "write" is Write and Edit; "Terminus" is the `mcp__terminus__recall_*` tools for recalling earlier sessions. No agent commits except `lead`, and no agent pushes.

## Layout

```
.claude-plugin/plugin.json        plugin manifest
.claude-plugin/marketplace.json   marketplace listing this repository as its one plugin
agents/<name>.md                  one role file per agent
```

## Install

Guild is a Claude Code plugin and its own marketplace:

```
/plugin marketplace add ohshitgorillas/guild
/plugin install guild@guild
```

Installed as a plugin, each agent is namespaced: `guild:architect`, `guild:lead`, and so on.

On the author's own machine the roster is used un-namespaced instead: `~/.claude/agents` is a symlink to this repository's `agents/` folder, so every project there sees the same agents and role files are edited only here.

The roles are written for the author's own machine and refer to paths and tools there (`~/.claude/...`, `~/dev/...`, Foreman, Relay, Terminus), so outside that setup they serve as reference more than as drop-in agents.
