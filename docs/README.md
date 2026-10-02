# Documentation index

| Document | Role | Evidence and limits |
|---|---|---|
| [README](../README.md) | Package scope, build, and verification status | Claims grounded in `package.json` and `src/` |
| [Security notes](../SECURITY.md) | Runtime permissions and safe deployment guidance | Source enables Hermes `--yolo`; not a security certification |
| [Development guide](../AGENTS.md) | Source layout and local integration notes | Recheck version-specific Paperclip steps |
| [Package manifest](../package.json) | Version, engines, dependencies, and scripts | Source of truth for package metadata |
| [Execution implementation](../src/server/execute.ts) | Hermes process invocation and Paperclip context | Review before changing permissions or execution behavior |
| [Environment checks](../src/server/test.ts) | Runtime prerequisite checks | Not a test suite |

## Configuration

The adapter configures the Hermes CLI path, model/provider, timeout, toolsets, session/worktree/checkpoint behavior, extra arguments, environment, and prompt template. Inspect `src/index.ts` and `src/server/execute.ts` for the current fields and defaults. Put credentials in Paperclip's secret handling or the host's protected environment, not in committed files.
