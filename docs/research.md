# Paperclip Ecosystem Research

> Community mods, plugins, tools, and resources — compiled for Nexoshape workflow.

## Plugins Worth Studying

### Memory & Intelligence

| Plugin | Stars | Description |
|--------|-------|-------------|
| [paperclip-plugin-hindsight](https://github.com/vectorize-io/hindsight) | — | Persistent long-term memory for agents — recall before every heartbeat, retain after every run. Vectorize-backed. |
| [paperclip-plugin-avp](https://github.com/creatorrmode-lead/paperclip-plugin-avp) | — | Agent Veil Protocol — DID identity, EigenTrust reputation, signed attestations. Trust gates before delegation. |

**Why it matters:** Our agents currently have no cross-session memory beyond Hermes MEMORY.md. Hindsight would give agents persistent recall across heartbeats. AVP would let agents build reputation scores.

### Communication & Notifications

| Plugin | Description |
|--------|-------------|
| [paperclip-plugin-discord](https://github.com/mvanhorn/paperclip-plugin-discord) | Bidirectional Discord: notifications, slash commands, community intel |
| [paperclip-plugin-slack](https://github.com/mvanhorn/paperclip-plugin-slack) | Slack notifications on issue creation/completion/approval |
| [paperclip-plugin-telegram](https://github.com/mvanhorn/paperclip-plugin-telegram) | Telegram notifications |

**Why it matters:** Currently Nexoshape has no notification channel. Slack/Telegram plugin would let us get alerts when agents complete work or need approval.

### Developer Tools

| Plugin | Description |
|--------|-------------|
| [paperclip-plugin-github-issues](https://github.com/mvanhorn/paperclip-plugin-github-issues) | Bidirectional GitHub Issues sync |
| [paperclip-plugin-acp](https://github.com/mvanhorn/paperclip-plugin-acp) | ACP runtime — runs Claude Code, Codex, Gemini CLI from any chat |
| [paperclip-mcp](https://github.com/wizarck/paperclip-mcp) | MCP server exposing Paperclip API as tools for Claude Code/Desktop |
| [obsidian-paperclip](https://github.com/istib/obsidian-paperclip) | Obsidian plugin to browse/comment/assign Paperclip issues |

**Why it matters:** GitHub sync would let Nexoshape agents interact with our repos. MCP server would let us query Paperclip from Hermes tools. ACP plugin supports more agent runtimes (Codex, Gemini CLI).

### UX & Workflow

| Plugin | Description |
|--------|-------------|
| [paperclip-aperture](https://github.com/tomismeta/paperclip-aperture) | Alternative focus view — ranks approvals, activity, events into now/next/ambient |
| [paperclip-plugin-company-wizard](https://github.com/yesterday-ai/paperclip-plugin-company-wizard) | AI-powered company setup with presets |
| [paperclip-plugin-chat](https://github.com/webprismdevin/paperclip-plugin-chat) | Interactive AI chat copilot for managing tasks/agents |
| [paperclip-plugin-writbase](https://github.com/Writbase/paperclip-plugin-writbase) | Bidirectional sync with WritBase tasks |

### Plugin Bundles

| Bundle | Description |
|--------|-------------|
| [oh-my-paperclip](https://github.com/gsxdsm/oh-my-paperclip) | Go-to bundle of plugins — curated by the awesome-paperclip maintainer |

## Adapter Ecosystem

### Official
- [hermes-paperclip-adapter](https://github.com/NousResearch/hermes-paperclip-adapter) (1,178★) — NousResearch official. **We use this.**

### Community Adapters
- [superbiche/paperclip-adapters](https://github.com/superbiche/paperclip-adapters) (7★) — Cline, Copilot, Qwen adapters. **Learn from their adapter-authoring notes:**
  - Uses external adapter plugin system (`POST /api/adapters/install`)
  - Each adapter exports `sessionManagement.supportsSessionResume: true`
  - CLI-native session handles round-trip through `agent_task_sessions`
  - Hot-install on Paperclip >= `3d15798` without restart
  - Documents gaps in Paperclip's plugin system at `docs/authoring-gaps.md`

**Key insight:** We could write a custom adapter for Nexoshape-specific workflows, following superbiche's pattern.

## Deployment Patterns

| Project | Stars | Approach |
|---------|-------|----------|
| [paperclip-desktop](https://github.com/aronprins/paperclip-desktop) | 487★ | Electron wrapper — bundles Paperclip as desktop app |
| [nexaddo/paperclip-ai-docker](https://github.com/nexaddo/paperclip-ai-docker) | 5★ | Docker image wrapper |
| [Made-By-Adem/paperclipai-docker](https://github.com/Made-By-Adem/paperclipai-docker) | 5★ | Docker + reverse-proxy |
| [birdcar/paperclip-docker](https://github.com/birdcar/paperclip-docker) | 2★ | Docker image builder |

## Learning Resources

### Books
- **[Headcount Zero](https://github.com/AnthonyDavidAdams/zero-employee-company-book)** (367★) — "How to Build an AI-Run Company with Paperclip" by Anthony David Adams
  - 13 chapters across 4 parts: Why → What → How → What If
  - Chapters 7-9 (Part 3: How) are most actionable: build first company, design org chart, governance
  - Open-source (CC BY-NC-SA 4.0)

### Community
- [awesome-paperclip](https://github.com/gsxdsm/awesome-paperclip) (648★) — Curated plugin/tool/resource list. Updated May 2026.
- [paperclip-discord-bot](https://github.com/rekon307/paperclip-discord-bot) (11★) — Community Discord bot with GitHub OAuth + AI summaries

## Novel Projects Using Paperclip

| Project | Description |
|---------|-------------|
| [f-crop/rustacean](https://github.com/f-crop/rustacean) | Rust project managed by AI agents via Paperclip governance |
| [hmzainjamil/hmz-paperclip-ceo-loop](https://github.com/hmzainjamil/hmz-paperclip-ceo-loop) | Autonomous 6-hour strategy + task review engine |
| [stapler](https://github.com/googlarz/stapler) (3★) | "Self-hosted AI org platform with compounding Quality Flywheel — every run gets graded, every failure becomes a rule" |
| [sangkny/CoOps-Platform](https://github.com/sangkny/CoOps-Platform) | Company Operations Platform under OpenClaw and PaperClip concepts |
| [Ayush765-spec/PaperclipaiOffline](https://github.com/Ayush765-spec/PaperclipaiOffline) | Offline Paperclip with local AI models |

## Actionable Takeaways for Nexoshape

1. **Install Hindsight plugin** — persistent agent memory would prevent the "from" session corruption and give agents long-term recall
2. **Add Telegram/Slack plugin** — notification channel for approvals and completions
3. **Study superbiche adapter pattern** — if we need a custom Nexoshape adapter
4. **Read Headcount Zero chapters 7-9** — practical guide to org chart design
5. **GitHub Issues plugin** — sync Paperclip issues with nexoshape-hermes-paperclip repo
6. **oh-my-paperclip** — evaluate the plugin bundle for our stack
