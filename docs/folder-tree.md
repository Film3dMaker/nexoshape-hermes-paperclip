# Nexoshape Unified Folder Tree

> Master directory map across all systems — Hermes, Paperclip, Nexoshape.

```
/root/
│
├── 📦 nexoshape-hermes-paperclip/       ← MASTER WORKFLOW REPO (github.com/Film3dMaker)
│   ├── README.md                        # Architecture, agents, patches, quick reference
│   └── docs/
│       └── research.md                  # Ecosystem deep-dive (plugins, adapters, books)
│
├── 🏭 noxoshape-workspace/              ← NEXOSHAPE MONOREPO (local only, no remote)
│   ├── apps/
│   │   ├── web/                         # Vite + React (nexoshape.com frontend)
│   │   │   ├── src/
│   │   │   ├── dist/
│   │   │   ├── server.js
│   │   │   ├── index.html
│   │   │   ├── submissions.jsonl        # 3D print submission data
│   │   │   └── package.json
│   │   ├── mobile/                      # React Native / Expo
│   │   │   ├── app/
│   │   │   ├── src/
│   │   │   ├── assets/
│   │   │   ├── app.json
│   │   │   └── package.json
│   │   ├── api/                         # Fastify backend (empty scaffold)
│   │   └── dashboard/                   # Admin dashboard (empty scaffold)
│   ├── packages/
│   │   ├── db/                          # Drizzle ORM + PostgreSQL schema
│   │   │   ├── src/
│   │   │   ├── drizzle.config.ts
│   │   │   └── package.json
│   │   ├── api-client/
│   │   ├── config/
│   │   ├── ui/
│   │   └── utils/
│   ├── docs/
│   │   ├── api/
│   │   ├── architecture/
│   │   └── guides/
│   ├── infra/
│   │   ├── docker/
│   │   └── terraform/
│   ├── scripts/
│   │   └── deploy-web.sh
│   ├── .github/
│   │   ├── workflows/
│   │   └── ISSUE_TEMPLATE/
│   ├── package.json
│   ├── pnpm-workspace.yaml
│   └── turbo.json
│
├── 🤖 hermes-workspace/                 ← HERMES WEB UI (local only, no remote)
│   ├── src/                             # Vite + React dashboard
│   ├── dist.bak/                        # Original dist (renamed — dev from source)
│   ├── *.ts / *.json                    # Patched: send-stream.ts, memory.ts, workspace.ts
│   └── package.json
│
├── 🧠 .hermes/                          ← HERMES AGENT CONFIG & DATA
│   ├── config.yaml                      # Provider (opencode-go/deepseek-v4-pro)
│   │                                    # + allow_private_urls: true
│   │                                    # + approvals.mode: smart
│   ├── .env                             # API keys
│   ├── profiles/
│   │   └── swarm1-12/                   # 12 swarm workers (7 deepseek, 5 owl-alpha)
│   ├── skills/
│   │   └── devops/
│   │       └── paperclip-deployment/    # SKILL.md + references + scripts
│   ├── sessions/                        # Hermes session transcripts
│   ├── memories/
│   │   └── MEMORY.md                    # Cross-session persistent memory
│   ├── plans/
│   ├── kanban.db
│   └── audio_cache/
│
├── 📊 .paperclip/                       ← PAPERCLIP INSTANCE DATA
│   └── instances/
│       └── default/
│           ├── config.json              # binds lan, port 3100
│           └── workspaces/
│               └── 27072407.../         # CTO agent workspace (empty)
│
├── 🏗️ /opt/paperclip/                   ← PAPERCLIP SOURCE (MIT, patched)
│   ├── server/
│   │   └── src/services/
│   │       └── heartbeat.ts             # PATCHED line 7846 (full session ID)
│   ├── ui/                              # React dashboard
│   ├── packages/
│   │   ├── db/                          # Drizzle schema + migrations
│   │   ├── shared/                      # Types, constants, validators
│   │   └── adapters/
│   ├── doc/                             # GOAL, PRODUCT, SPEC, DATABASE
│   ├── scripts/
│   │   └── reset-paperclip-password.py  # Scrypt password reset
│   ├── .env                             # DATABASE_URL=postgres://...
│   └── package.json
│
└── 📚 /root/
    ├── nexoshape-profile.md             # Company facts (materials, services, contact)
    └── voice-memos/                     # TTS output cache
```

## System Services

```
Port  Service               Systemd Unit
────  ────────────────────  ──────────────────────────
3000  hermes-workspace      (pnpm dev, no systemd yet)
3100  paperclip             paperclip.service
5432  postgresql            postgresql.service
8642  hermes gateway        hermes-gateway.service (user)
9119  hermes dashboard      hermes-dashboard.service (user)
```

## Agents

```
Agent  ID                                    Runtime        Host
─────  ────────────────────────────────────  ────────────   ──────────────────────────
CEO    2a3ee4d9-6b50-47cb-ba12-b87e0edf9e33  hermes_local   hermes chat -q (--yolo, 600s)
CTO    27072407-7fb0-4771-8722-2db86e16e40c  hermes_local   hermes chat -q (--yolo, 600s)
```

## Key Databases

```
Database     Location                          Tables
────────     ────────────────────────────────  ─────────────────
paperclip    postgres://paperclip@:5432        85 tables (agents, issues, heartbeat_runs, account...)
kanban       ~/.hermes/kanban.db               SQLite (swarm task board)
memories     ~/.hermes/memories/MEMORY.md       Markdown (Hermes persistent memory)
sessions     ~/.hermes/sessions/                JSON transcript archives
```
