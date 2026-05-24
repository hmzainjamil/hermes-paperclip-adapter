# hermes-paperclip-adapter

> **Wire Hermes Agent as a Paperclip AI employee** — autonomous goal execution with budget guardrails, 30+ tools, and structured transcript rendering

<p align="center">
  <a href="https://github.com/hmzainjamil/hermes-paperclip-adapter/stargazers"><img src="https://img.shields.io/github/stars/hmzainjamil/hermes-paperclip-adapter?style=for-the-badge&labelColor=555&color=yellow" alt="Stars"/></a>
  <a href="https://github.com/hmzainjamil/hermes-paperclip-adapter/network/members"><img src="https://img.shields.io/github/forks/hmzainjamil/hermes-paperclip-adapter?style=for-the-badge&labelColor=555&color=blue" alt="Forks"/></a>
  <a href="https://github.com/hmzainjamil/hermes-paperclip-adapter/issues"><img src="https://img.shields.io/github/issues/hmzainjamil/hermes-paperclip-adapter?style=for-the-badge&labelColor=555&color=red" alt="Issues"/></a>
  <a href="https://github.com/hmzainjamil/hermes-paperclip-adapter/pulls"><img src="https://img.shields.io/github/issues-pr/hmzainjamil/hermes-paperclip-adapter?style=for-the-badge&labelColor=555&color=purple" alt="PRs"/></a>
  <a href="https://github.com/hmzainjamil/hermes-paperclip-adapter/commits/main"><img src="https://img.shields.io/github/last-commit/hmzainjamil/hermes-paperclip-adapter?style=for-the-badge&labelColor=555&color=green" alt="Last Commit"/></a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.11+-blue?style=flat&labelColor=555&logo=python"/>
  <img src="https://img.shields.io/badge/Hermes_Agent-NousResearch-orange?style=flat&labelColor=555"/>
  <img src="https://img.shields.io/badge/Paperclip_AI-integration-green?style=flat&labelColor=555"/>
  <img src="https://img.shields.io/badge/License-MIT-lightgrey?style=flat&labelColor=555"/>
  <img src="https://img.shields.io/badge/Providers-8-purple?style=flat&labelColor=555"/>
</p>

---

## Why This Exists

Paperclip AI runs companies through AI employees that execute tasks autonomously. Hermes Agent (NousResearch) is the most capable open-source agent with 30+ native tools, persistent memory, and 80+ skills. This adapter bridges the two: Paperclip assigns tasks → Hermes executes → structured results flow back into Paperclip's issue tracker.

Without this adapter you'd need to manually relay task context between systems, losing audit trails, budget controls, and structured output rendering. With it, Hermes operates as a first-class Paperclip team member — indistinguishable from a human employee in the UI.

---

## At a Glance

| Capability | Detail |
|---|---|
| Inference providers | 8: Anthropic, OpenRouter, OpenAI, Nous, Codex, ZAI, Kimi Coding, MiniMax |
| Skills integration | Scans `~/.hermes/skills/` + Paperclip-managed skills with sync/list/resolve API |
| Transcript parsing | Raw Hermes stdout → typed `TranscriptEntry` objects with tool cards |
| Output post-processing | ASCII banners, setext headings, `+--+` tables → clean GFM markdown |
| Wake triggers | Comment-driven wakes on issue comments, not just task assignments |
| Auto model detection | Reads `~/.hermes/config.yaml` → pre-populates UI with configured model |
| Session codec | Structured validation + migration of session state across heartbeats |
| Stderr reclassification | MCP init messages / structured logs → not treated as errors |
| Session source tagging | Tagged `tool` source → doesn't pollute interactive history |
| Filesystem checkpoints | `--checkpoints` flag for rollback safety on destructive tasks |
| Thinking effort control | `--reasoning-effort` passthrough for reasoning models |
| Budget guardrails | Per-task token budget enforcement with overage alerts to Paperclip |

---

## 🧠 CONCEPTS

| Concept | What It Does |
|---|---|
| **TranscriptEntry** | Typed object representing one step: tool call, tool result, assistant message, or error |
| **Session codec** | Serializes/deserializes Hermes session state for heartbeat continuity |
| **Wake mechanism** | Polls Paperclip `/api/companies/{CID}/issues` for comment events → spawns Hermes |
| **Skills resolver** | Merges Paperclip skill registry with `~/.hermes/skills/` — deduplicates by slug |
| **Stderr classifier** | Regex rules determine if stderr line is benign (MCP init) or real error |
| **Model auto-detect** | Parses YAML config → injects provider + model into Paperclip task form |
| **Budget enforcer** | Counts tokens per run, posts warning comment if >80% budget consumed |
| **Checkpoint manager** | Snapshots file tree before destructive operations → enables rollback |

### 🔥 Hot

- **Structured transcript rendering** — every Hermes tool call renders as expandable card in Paperclip UI, not raw text dump
- **Comment-driven wakes** — mention `@hermes` in any issue comment → agent wakes, reads full thread, executes
- **Cross-provider budget routing** — automatically switches to cheaper provider when budget threshold hit mid-task
- Source → [HMZ](https://github.com/hmzainjamil)

---

## ⚙️ HOW IT WORKS

```
Paperclip Issue Created
        ↓
adapter/wake.py polls /api/companies/{CID}/issues
        ↓
Task payload → HermesTaskRunner.build_prompt()
        ↓
hermes run --task "..." --budget 50000 --checkpoints
        ↓
stdout captured → TranscriptParser.parse_lines()
        ↓
TranscriptEntry[] → Paperclip comment API (structured JSON)
        ↓
Issue status updated → DONE / BLOCKED
```

**Session heartbeat:**
```
Every 30s: session_codec.snapshot() → POST /api/companies/{CID}/issues/{ID}/heartbeat
On resume:  session_codec.restore() → Hermes continues from checkpoint
```

---

## 🚀 INSTALL

**Prerequisites:**
- Python 3.11+
- Hermes Agent installed: `pip install hermes-agent` or from source
- Paperclip running locally: `http://127.0.0.1:3100`
- At least one inference provider API key

```bash
git clone https://github.com/hmzainjamil/hermes-paperclip-adapter
cd hermes-paperclip-adapter
pip install -r requirements.txt

# Configure
cp .env.example .env
# Edit .env: PAPERCLIP_URL, PAPERCLIP_COMPANY_ID, ANTHROPIC_API_KEY, etc.

# Test connection
python adapter/health_check.py
```

---

## 📟 USAGE

```bash
# Run adapter (polls for tasks continuously)
python adapter/main.py --company-id YOUR_CID

# Run single task manually
python adapter/run_task.py --issue-id 123 --budget 100000

# List available skills (merged Paperclip + Hermes)
python adapter/skills.py list

# Sync skills to Paperclip
python adapter/skills.py sync

# View session transcript
python adapter/transcripts.py --issue-id 123

# Rollback to checkpoint
python adapter/checkpoints.py restore --issue-id 123 --checkpoint latest
```

---

## ⚙️ CONFIGURATION

| Variable | Default | Description |
|---|---|---|
| `PAPERCLIP_URL` | `http://127.0.0.1:3100` | Paperclip local server URL |
| `PAPERCLIP_COMPANY_ID` | required | Your Paperclip company UUID |
| `HERMES_CONFIG` | `~/.hermes/config.yaml` | Hermes config path for model auto-detect |
| `HERMES_SKILLS_DIR` | `~/.hermes/skills/` | Skills directory to scan and merge |
| `DEFAULT_PROVIDER` | `anthropic` | Inference provider for new tasks |
| `DEFAULT_MODEL` | `claude-sonnet-4-5` | Model slug when not specified in task |
| `BUDGET_WARNING_PCT` | `0.8` | Alert threshold as fraction of task budget |
| `POLL_INTERVAL_SEC` | `10` | Seconds between Paperclip issue polls |
| `CHECKPOINT_ENABLED` | `true` | Enable filesystem checkpoints |
| `CHECKPOINT_DIR` | `~/.hermes/checkpoints/` | Checkpoint storage directory |
| `TRANSCRIPT_FORMAT` | `structured` | `structured` (tool cards) or `raw` (plain text) |
| `STDERR_RECLASSIFY` | `true` | Enable benign stderr reclassification |
| `REASONING_EFFORT` | `auto` | `low` / `medium` / `high` / `auto` for thinking models |
| `MAX_RETRIES` | `3` | Task retry count on non-fatal errors |
| `SESSION_TIMEOUT_MIN` | `60` | Session TTL before forced checkpoint + pause |

---

## 💡 TIPS AND TRICKS

### Provider Routing
1. **Cost optimization** — set `DEFAULT_PROVIDER=openrouter` and configure model routing rules to use cheapest model that fits task complexity. Source → [HMZ](https://github.com/hmzainjamil)
2. **Kimi for long context** — tasks with >100K token context (large codebases) → force Kimi Coding provider with `--provider kimi`. Source → [HMZ](https://github.com/hmzainjamil)
3. **MiniMax fallback** — configure as last-resort provider for when all paid APIs are rate-limited. Source → [HMZ](https://github.com/hmzainjamil)

### Skills Management
4. **Skill priority** — Paperclip-managed skills override Hermes native ones with same slug. Control priority in `skills_resolver.py`. Source → [HMZ](https://github.com/hmzainjamil)
5. **Bulk sync** — `python adapter/skills.py sync --all` syncs all 80+ Hermes skills to Paperclip in one shot. Source → [HMZ](https://github.com/hmzainjamil)
6. **Skill scoping** — restrict which skills are available per task via Paperclip issue labels `skill:python` `skill:git`. Source → [HMZ](https://github.com/hmzainjamil)

### Session Management
7. **Checkpoint diff** — `python adapter/checkpoints.py diff --issue-id 123` shows file changes between checkpoints. Source → [HMZ](https://github.com/hmzainjamil)
8. **Forced resume** — if session crashes mid-task, `python adapter/run_task.py --issue-id 123 --resume` picks up from last checkpoint. Source → [HMZ](https://github.com/hmzainjamil)
9. **Session search** — Hermes FTS5 search over past sessions: `hermes sessions search "keyword"` to find prior context. Source → [HMZ](https://github.com/hmzainjamil)

### Debugging
10. **Transcript replay** — `python adapter/transcripts.py replay --issue-id 123 --step 5` reruns from step 5. Source → [HMZ](https://github.com/hmzainjamil)
11. **Stderr audit** — `STDERR_RECLASSIFY=false` to see all raw stderr output for debugging MCP connection issues. Source → [HMZ](https://github.com/hmzainjamil)
12. **Budget trace** — `--budget-trace` flag logs per-tool token consumption to identify expensive operations. Source → [HMZ](https://github.com/hmzainjamil)

---

## 🔧 TROUBLESHOOTING

| Issue | Cause | Fix |
|---|---|---|
| `ConnectionRefusedError: 3100` | Paperclip not running | `paperclip start` or check `http://127.0.0.1:3100/health` |
| `hermes: command not found` | Hermes not on PATH | `pip install hermes-agent` or add to PATH |
| Transcripts show as raw text | Old adapter version | Upgrade: transcript parser added in v1.2 |
| Agent wakes but never completes | Budget exhausted | Increase `--budget` or check token usage in Paperclip issue |
| Skills not showing in Paperclip | Sync not run | `python adapter/skills.py sync` |
| Model auto-detect shows wrong model | Config path wrong | Set `HERMES_CONFIG` env var to correct path |
| Checkpoint restore fails | Corrupted checkpoint | `python adapter/checkpoints.py clean --issue-id 123` |
| MCP errors in transcript | Benign init noise | Already filtered — check `STDERR_RECLASSIFY=true` |

---

## 📊 ARCHITECTURE

```
hermes-paperclip-adapter/
├── adapter/
│   ├── main.py              # Entry: polls Paperclip, dispatches tasks
│   ├── wake.py              # Comment/assignment wake detection
│   ├── run_task.py          # Single-task runner with budget enforcement
│   ├── transcript_parser.py # stdout → TranscriptEntry[]
│   ├── session_codec.py     # Session state serialization
│   ├── skills_resolver.py   # Paperclip + Hermes skill merger
│   ├── stderr_classifier.py # Benign vs real error classification
│   ├── checkpoints.py       # Filesystem checkpoint manager
│   └── health_check.py      # Connectivity validator
├── providers/
│   ├── anthropic.py
│   ├── openrouter.py
│   ├── kimi.py
│   └── minimax.py
├── tests/
│   ├── test_transcript_parser.py
│   └── test_session_codec.py
├── .env.example
└── requirements.txt
```

---

## 🗺️ ROADMAP

- [ ] Parallel task execution (multiple Hermes workers per company)
- [ ] Webhook-based wake (replace polling with Paperclip webhook push)
- [ ] Cost analytics dashboard — per-task, per-provider breakdown
- [ ] Skill marketplace sync — pull skills from Anthropic Skills Store
- [ ] Multi-company support — one adapter instance serving multiple Paperclip orgs
- [ ] Web UI for transcript review and replay
- [ ] Automated budget optimization — ML-based provider selection per task type

---

## ☠️ STARTUPS / BUSINESSES

**Running a digital agency or SaaS on Paperclip?**

This adapter lets you scale to unlimited AI employees without scaling headcount. Each Hermes instance handles tasks with the full capability of a senior engineer — code, research, reporting, client communication.

Agencies using this pattern: assign client deliverable → Hermes executes → Paperclip tracks → invoice generated. Zero human-in-the-loop for standard deliverables.

**Production deployment:**
```bash
# LaunchAgent (macOS)
cp launchd/com.paperclip.hermes-adapter.plist ~/Library/LaunchAgents/
launchctl load ~/Library/LaunchAgents/com.paperclip.hermes-adapter.plist

# systemd (Linux)
cp systemd/hermes-adapter.service /etc/systemd/system/
systemctl enable --now hermes-adapter
```

---

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=hmzainjamil/hermes-paperclip-adapter&type=Date)](https://star-history.com/#hmzainjamil/hermes-paperclip-adapter&Date)

---

<p align="center">
  Built by <a href="https://github.com/hmzainjamil">HMZ</a> · <a href="https://github.com/hmzainjamil/hermes-paperclip-adapter/issues">Report Bug</a> · <a href="https://github.com/hmzainjamil/hermes-paperclip-adapter/pulls">Contribute</a>
</p>

---

## Comparison: Hermes vs Claude Code vs Codex as Paperclip Employees

| Feature | Claude Code | Codex | Hermes Agent |
|---|---|---|---|
| Persistent memory | ❌ | ❌ | ✅ Remembers across sessions |
| Native tools | ~5 | ~5 | 30+ (terminal, file, web, browser, vision, git, etc.) |
| Skills system | ❌ | ❌ | ✅ 80+ loadable skills |
| Session search | ❌ | ❌ | ✅ FTS5 search over past conversations |
| Providers | 1 | 1 | 8 (Anthropic, OpenRouter, OpenAI, Nous, Codex, ZAI, Kimi, MiniMax) |
| Paperclip integration | Partial | ❌ | ✅ Full via this adapter |
| Budget enforcement | ❌ | ❌ | ✅ Per-task with alerts |
| Transcript rendering | Basic | Basic | ✅ Structured tool cards |
| Comment-driven wakes | ❌ | ❌ | ✅ |
| Filesystem checkpoints | ❌ | ❌ | ✅ |

---

## Related Projects

- [Paperclip AI](https://paperclip.ing) — zero-human company OS
- [Hermes Agent](https://github.com/NousResearch/hermes-agent) — NousResearch agent runtime
- [claude-ai-system](https://github.com/hmzainjamil/claude-ai-system) — MAE orchestrator + Tier 0 routing
- [awesome-claude-agents](https://github.com/hmzainjamil/awesome-claude-agents) — curated agent list

