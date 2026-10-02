# Hermes Paperclip Adapter

A TypeScript Paperclip adapter that launches the Hermes CLI as an employee process. It provides Paperclip adapter exports, server execution and environment checks, session handling, skill synchronization exports, and UI/CLI helpers.

## Status and requirements

| Item | Source evidence |
|---|---|
| Package version field | `0.3.0` in `package.json`; this is a manifest value, not a release claim |
| Language/build | TypeScript; `tsc` build script |
| Node.js | `>=20` from `package.json` |
| Runtime dependencies | Paperclip adapter utilities and Hermes Agent CLI |
| Configuration | Adapter config includes model/provider, timeout, toolsets, session/worktree/checkpoint options, CLI path, environment, and prompt template |
| Automated test scripts | None in the package scripts; `src/server/test.ts` checks runtime prerequisites |
| Verification | No build, typecheck, or runtime was run for this documentation update |

See [configuration and execution notes](docs/README.md) and [security notes](SECURITY.md).

## How it works

The server adapter starts `hermes chat` as a child process, streams/parses output, and returns an execution result to Paperclip. It can pass task/session context and Paperclip environment values to the Hermes process. The adapter reads the local Hermes model configuration for model/provider detection.

This is a connector between two independently configured systems. The code here does not install or configure Paperclip, Hermes, providers, or API credentials.

## Build

Requires Node.js 20 or newer, npm, and TypeScript dependencies from the lockfile:

```bash
npm ci
npm run build
```

The package also declares `npm run typecheck` and `npm run lint`. These commands are documented from `package.json`; none were executed for this README change. There is no `test` script in the package manifest.

For local integration, see [AGENTS.md](AGENTS.md) and verify its steps against the Paperclip version you use.

## Security boundary

The adapter adds `--yolo` to the Hermes CLI arguments in `src/server/execute.ts`, bypassing Hermes dangerous-command approval prompts. Treat each run as an autonomous process with the permissions of its host account. Use a dedicated least-privilege account, restrict available toolsets and writable paths, isolate work, and inspect output and logs. See [SECURITY.md](SECURITY.md) before connecting a Paperclip agent.

## Scope and limits

- Successful compilation does not prove compatibility with a live Paperclip or Hermes release.
- API keys, provider access, Paperclip endpoints, and model availability are configured externally.
- No budget enforcement, audit guarantee, or runtime success is asserted by this README; verify each property in the connected system.
- The repository includes a source-level environment check, not a complete integration test suite.
