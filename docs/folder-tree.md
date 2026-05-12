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
├── 🏭 nexoshape-workspace/              ← NEXOSHAPE BUSINESS WORKSPACE
│   ├── README.md                        # Structure overview
│   ├── web/                             # Instant Quote Engine (Vite + React + Express)
│   │   ├── src/
│   │   │   ├── App.tsx                  # 4-step wizard
│   │   │   ├── main.tsx
│   │   │   ├── types.ts                 # 7 materials, pricing
│   │   │   ├── index.css
│   │   │   └── components/
│   │   │       ├── FileUpload.tsx
│   │   │       ├── MaterialSelector.tsx
│   │   │       ├── PriceEstimate.tsx
│   │   │       └── QuoteForm.tsx
│   │   ├── dist/
│   │   ├── server.js                    # Express API + Formspree integration
│   │   ├── index.html
│   │   └── package.json
│   ├── app/                             # Mobile + workflow docs
│   │   └── nexoshape-hermes-paperclip/  # Workflow documentation repo
│   ├── orders/                          # Quotes, invoices, job tracking
│   ├── customers/                       # CRM data
│   ├── products/                        # Catalog, pricing sheets
│   ├── finances/                        # Budgets, revenue, taxes
│   ├── marketing/                       # Ads, social, branding
│   ├── operations/                      # Logistics, suppliers, shipping
│   ├── design/                          # 3D models, STL files, slicer configs
│   ├── agents/                          # Paperclip CEO/CTO working output
│   ├── legal/                           # Contracts, terms, privacy
│   └── docs/                            # Company documentation
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
3001  nexoshape-web         nexoshape-web.service
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
