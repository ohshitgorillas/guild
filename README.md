# Guild

Guild is the shared roster of Claude Code subagents that Relay and Gauntlet dispatch. Keeping the agents in one repository lets those projects be audited together with the roles they depend on.

## Agents

| Agent | Role | Model | Effort |
|---|---|---|---|
| `architect` | Writes one design file per dispatch, or records rulings and approval on one; never builds. | opus | medium |
| `divvyer` | Cuts an approved design into the build queue, and re-cuts it after a stop. | opus | medium |
| `lead` | Runs one workstream in its own worktree: dispatches workers, re-runs done-checks, commits. | opus | (not set) |
| `builder` | Builds one Order step of an approved design within the paths it lists. | sonnet | medium |
| `core-builder` | Gauntlet: builds one step in the pure layer, `gauntlet/core/`. | sonnet | medium |
| `seamstress` | Gauntlet: builds one step in the thin I/O layer, `gauntlet/seams/`. | sonnet | medium |
| `gatesmith` | Gauntlet: builds gates, build configuration and the pre-commit hook. | sonnet | medium |
| `test-writer` | Writes the repository's test files, one step per dispatch. | opus | medium |
| `mutant-hunter` | Kills one surviving mutmut mutant with a new test, or rules it equivalent. | sonnet | medium |
| `diff-reviewer` | Blind reviewer of a diff's code, build files and test helpers. | opus | medium |
| `test-reviewer` | Blind reviewer of a diff's test files. | opus | medium |
| `juror` | Foreman: read-only judge of a check that cannot reach green. | opus | medium |
| `spiker` | Answers one question with a throwaway experiment in the scratchpad. | sonnet | medium |
| `role-author` | Writes one agent role prompt per dispatch. | opus | medium |

## Install

Guild is a Claude Code plugin and its own marketplace:

```
/plugin marketplace add ohshitgorillas/guild
/plugin install guild@guild
```

Installed as a plugin, each agent is namespaced: `guild:architect`, `guild:lead`, and so on.

The roles are written for the author's own machine and refer to paths and tools there (`~/.claude/...`, `~/dev/...`, Foreman, Terminus), so outside that setup they serve as reference more than as drop-in agents.
