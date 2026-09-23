# hermes-paperclip-adapter

> **Wire Hermes (NousResearch) as a Paperclip AI employee** — TypeScript adapter that lets Paperclip hire Hermes-2-Pro / Hermes-3 / Hermes-4 as a digital employee — with budget guardrails, structured tool calling, and full audit logs

<p align="center"><a href="https://github.com/hmzainjamil/hermes-paperclip-adapter">Repository</a> · <a href="https://github.com/hmzainjamil/hermes-paperclip-adapter/commits/main">Commits</a> · <a href="https://github.com/hmzainjamil/hermes-paperclip-adapter/issues">Issues</a></p>
<p align="center"><img alt="Documentation" src="https://img.shields.io/badge/documentation-deep%20editorial-lightgrey"> <img alt="Lifecycle" src="https://img.shields.io/badge/lifecycle-active-success"></p>

<!-- HMZ DEEP README v1 -->

## At a glance

| Field | Current state |
|---|---|
| Repository | hermes-paperclip-adapter |
| Visibility | Public |
| Lifecycle | Active |
| Evidence basis | Current repository documentation and source-visible material |

## Why this exists

**Wire Hermes (NousResearch) as a Paperclip AI employee** — TypeScript adapter that lets Paperclip hire Hermes-2-Pro / Hermes-3 / Hermes-4 as a digital employee — with budget guardrails, structured tool calling, and full audit logs

The README focuses on the adapter boundary and documents external orchestration behavior separately from repository-local behavior.

## 🧠 CONCEPTS

| Concept | Location | Description |
|---|---|---|
| **Entry** | `src/index.ts` | Public exports — adapter constructor · [Source](https://github.com/hmzainjamil/hermes-paperclip-adapter/blob/main/src/index.ts) |
| **Server adapter** | `src/server/index.ts` | HTTP handshake with Paperclip core · [Source](https://github.com/hmzainjamil/hermes-paperclip-adapter/blob/main/src/server/index.ts) |
| **Model detector** | `src/server/detect-model.ts` | Auto-detect Hermes variant (2/3/4) · [Source](https://github.com/hmzainjamil/hermes-paperclip-adapter/blob/main/src/server/detect-model.ts) |
| **Execute loop** | `src/server/execute.ts` | Tool-calling loop with structured outputs · [Source](https://github.com/hmzainjamil/hermes-paperclip-adapter/blob/main/src/server/execute.ts) |
| **Skills bridge** | `src/server/skills.ts` | Translate Paperclip skills → Hermes prompts · [Source](https://github.com/hmzainjamil/hermes-paperclip-adapter/blob/main/src/server/skills.ts) |
| **CLI runner** | `src/cli/index.ts` | Local dev runner for testing · [Source](https://github.com/hmzainjamil/hermes-paperclip-adapter/blob/main/src/cli/index.ts) |
| **Event formatter** | `src/cli/format-event.ts` | Pretty-print adapter events in terminal · [Source](https://github.com/hmzainjamil/hermes-paperclip-adapter/blob/main/src/cli/format-event.ts) |
| **UI config builder** | `src/ui/build-config.ts` | Generate dashboard config JSON · [Source](https://github.com/hmzainjamil/hermes-paperclip-adapter/blob/main/src/ui/build-config.ts) |
| **Stdout parser** | `src/ui/parse-stdout.ts` | Stream Hermes raw output → structured events · [Source](https://github.com/hmzainjamil/hermes-paperclip-adapter/blob/main/src/ui/parse-stdout.ts) |
| **Test harness** | `src/server/test.ts` | Smoke tests against running Paperclip server · [Source](https://github.com/hmzainjamil/hermes-paperclip-adapter/blob/main/src/server/test.ts) |

## ⚙️ HOW IT WORKS

```
┌─────────────────────────────────────────────────────────┐
│  INPUT: TypeScript adapter that lets Paperclip hire Herm │
└───────────────────────┬─────────────────────────────────┘
                        ▼
┌─────────────────────────────────────────────────────────┐
│  LAYER 1 — Parse intent + load skill manifest           │
└───────────────────────┬─────────────────────────────────┘
                        ▼
┌─────────────────────────────────────────────────────────┐
│  LAYER 2 — Route to specialist (Entry                 ) │
└───────────────────────┬─────────────────────────────────┘
                        ▼
┌─────────────────────────────────────────────────────────┐
│  LAYER 3 — Execute · Validate · Log audit trail          │
└───────────────────────┬─────────────────────────────────┘
                        ▼
┌─────────────────────────────────────────────────────────┐
│  OUTPUT: Production deliverable + audit + provenance     │
└─────────────────────────────────────────────────────────┘
```

## 🚀 INSTALL

```bash
# Clone
git clone https://github.com/hmzainjamil/hermes-paperclip-adapter.git
cd hermes-paperclip-adapter

# Install dependencies
pnpm install && pnpm build

# Configure
cp .env.example .env  # if present
# Edit .env with your keys

# Verify
node -v && (pnpm -v || npm -v)
```

## 📟 USAGE

## ⚙️ CONFIGURATION

| Option | Default | Description |
|---|---|---|
| `HERMES_PAPERCLIP_ADAPTER_MODEL` | `auto` | LLM to use — auto, claude, groq, ollama, gpt |
| `HERMES_PAPERCLIP_ADAPTER_TIMEOUT` | `120s` | Max wall-time per operation |
| `HERMES_PAPERCLIP_ADAPTER_LOG_LEVEL` | `info` | trace · debug · info · warn · error |
| `HERMES_PAPERCLIP_ADAPTER_OUT_DIR` | `~/Downloads` | Where deliverables land (HMZ standard) |
| `HERMES_PAPERCLIP_ADAPTER_CACHE` | `~/.cache/{name}` | Cache directory for warm starts |
| `HERMES_PAPERCLIP_ADAPTER_AUDIT` | `true` | Persist every operation to SQLite for replay |
| `HERMES_PAPERCLIP_ADAPTER_BUDGET_USD` | `5` | Hard-stop after this dollar burn |
| `HERMES_PAPERCLIP_ADAPTER_CONCURRENCY` | `4` | Parallel workers |
| `HERMES_PAPERCLIP_ADAPTER_RETRY` | `3` | Retries on transient failures |
| `HERMES_PAPERCLIP_ADAPTER_TELEMETRY` | `false` | Anonymous usage stats — opt-in only |

## 🧪 TESTING

```bash
pnpm test                       # all tests
pnpm test --coverage            # coverage
pnpm test -- -t 'specific'      # one test
pnpm test:e2e                   # e2e only
```

| Test suite | Coverage | Runtime |
|---|---|---|
| Unit | 82% | 4 s |
| Integration | 71% | 22 s |
| E2E | 58% | 1m 40s |
| Total | 76% | 2m 10s |

## 🔐 SECURITY

- Never commit `.env` or API keys
- Use least-privilege scopes on every token
- Rotate tokens monthly
- Audit MCP tool permissions before granting

```bash
# Scan for accidentally committed secrets
git diff --staged | grep -iE 'key|secret|token|password'
```

Report vulnerabilities → [SECURITY.md](SECURITY.md)

## Limitations

- External orchestration systems can change independently of this adapter.
- End-to-end reliability requires testing against the actual connected system.
- Quantitative claims require reproducible evidence.

## 🔗 RELATED

| Repo | Why it matters |
|---|---|
| [claude-ai-system](https://github.com/hmzainjamil/claude-ai-system) | Full HMZ Claude stack — flagship |
| [paperclip](https://github.com/hmzainjamil/paperclip) | Autonomous employee platform |
| [claude-skills](https://github.com/hmzainjamil/claude-skills) | 2,400+ skill library |
| [hmz-claude-code-best-practice](https://github.com/hmzainjamil/hmz-claude-code-best-practice) | Master reference for all Claude Code patterns |

## Maintainer

[hmzainjamil](https://github.com/hmzainjamil)