# Noxoshape Hermes Paperclip Workflow

> **"We don't tell agents what to do. We tell them who to be."**

Autonomous AI company infrastructure: Paperclip (control plane) orchestrates Hermes Agent (execution engine) to run Nexoshape (3D printing company) on a Hostinger VPS via Tailscale.

---

## Architecture

```
┌───────────────────────────────────────────────────────────┐
│                   Hostinger VPS (Ubuntu Noble)            │
│                                                           │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────────┐ │
│  │  Paperclip   │  │    Hermes    │  │    PostgreSQL     │ │
│  │  :3100       │──│  Gateway     │  │    :5432          │ │
│  │  control     │  │  :8642       │  │    paperclip db   │ │
│  │  plane       │  │              │  └──────────────────┘ │
│  └──────┬───────┘  └──────┬───────┘                       │
│         │                 │                               │
│    CEO agent         ┌────┴────┐                          │
│    CTO agent         │ Hermes  │                          │
│                      │ Workspace│                          │
│                      │ :3000    │                          │
│                      └─────────┘                          │
│                                                           │
│  Access: Tailscale 100.78.152.119                         │
└───────────────────────────────────────────────────────────┘
```

## The Stack

| Component | Port | Role |
|-----------|------|------|
| **Paperclip** | 3100 | AI company control plane — agents, issues, inbox |
| **Hermes Gateway** | 8642 | Agent execution API |
| **Hermes Dashboard** | 9119 | Agent monitoring (127.0.0.1 only) |
| **Hermes Workspace** | 3000 | Web UI + zero-fork proxy |
| **PostgreSQL** | 5432 | Paperclip persistence |
| **Tailscale** | — | Secure remote access |

## Agents

| Agent | Role | Adapter | Config |
|-------|------|---------|--------|
| **CEO** | Strategy & delegation | `hermes_local` | `--yolo`, 600s timeout |
| **CTO** | Engineering execution | `hermes_local` | `--yolo`, 600s timeout |

Both agents auto-detect the provider/model from `~/.hermes/config.yaml` (currently: opencode-go / deepseek-v4-pro).

## The Heartbeat Loop

```
Paperclip timer fires (every 300s)
  → spawns hermes chat -q + heartbeat prompt
  → agent queries Paperclip API via curl
  → if assigned work: executes task
  → posts result back to issue
  → session persisted for next heartbeat
```

## Critical Patches Applied

### 1. `heartbeat.ts:7846` — Full Session ID Persistence

Paperclip's hermes_local adapter truncates session IDs to 16 characters, breaking `hermes --resume`.

```typescript
// BEFORE (broken):
sessionIdAfter: nextSessionState.displayId ?? nextSessionState.legacySessionId,

// AFTER (fixed):
sessionIdAfter: (nextSessionState.params?.sessionId as string) ?? nextSessionState.displayId ?? nextSessionState.legacySessionId,
```

File: `/opt/paperclip/server/src/services/heartbeat.ts`

### 2. Hermes Workspace Patches (v2.3.0)

Three patches required for root-user VPS:
- `send-stream.ts` — prevent SSE crash on cancel
- `memory.ts` — local memory-browser fallback
- `workspace.ts` — unblock `/root` and `/usr` from file browser

Files in: `~/hermes-workspace/`

## Security: The `--yolo` Requirement

Agents run in non-interactive `hermes chat -q` mode. The adapter prompt uses `curl | python3` pipes to access the Paperclip API. Hermes Tirith flags three issues:

1. [MEDIUM] Raw IP address (`100.78.152.119`)
2. [HIGH] Plain HTTP
3. [HIGH] `curl | python3` pipe-to-interpreter

Three config changes alone aren't sufficient:
- `security.allow_private_urls: true`
- `approvals.mode: smart`  
- `auxiliary.default` model configured

The `[o]nce/[s]ession/[d]eny` prompt appears in `-q` with nobody to answer → denied. **`--yolo` in agent extra args is mandatory.**

## The Recovery Cascade (Death Spiral)

When agents fail repeatedly on an assigned issue:

```
Agent assigned NEX-3
  → times out (Tirith blocking, short timeout, etc.)
  → Paperclip: "stranded_assigned_issue"
  → creates NEX-4 "Recover stalled NEX-3"
  → NEX-4 also fails (same root cause)
  → creates NEX-5 "Recover stalled NEX-3" 
  → Paperclip: "stopped automatic stranded-work recovery"
  → ALL issues blocked, agents idle
```

**Fix checklist:**
1. `security.allow_private_urls: true` + `hermes gateway restart`
2. `--yolo` in agent Extra args
3. Timeout ≥ 600s
4. NULL corrupted `session_id` in `agent_runtime_state` if stuck in "from" loop
5. Manual heartbeat to kick off fresh run

## The `"from"` Session Corruption Bug

The hermes-paperclip-adapter v0.2.0 parses the word "from" as a session ID from Hermes error output:

```
Hermes: "Session not found: from"
Adapter: SESSION_ID_REGEX captures "from"
  → stored as session_id = "from"
  → next run: --resume from
  → fails immediately
  → parses "from" again
  → infinite loop
```

**Fix:**
```sql
-- Check
SELECT session_id FROM agent_runtime_state WHERE agent_id = '<id>';

-- If it says "from", clear it
UPDATE agent_runtime_state SET session_id = NULL WHERE agent_id = '<id>';
```

Then trigger a manual heartbeat. The fresh run starts without `--resume` and stores a valid session ID.

## Password Management

Paperclip's better-auth hashes passwords with scrypt (N=16384, r=16, p=1, dkLen=64). Two gotchas:
- Salt is hex-encoded then UTF-8-bytes-passed to scrypt (not hex-decoded)
- Password is NFKC-normalized before hashing

```python
import hashlib, os, unicodedata

pw = unicodedata.normalize('NFKC', 'YOUR_PASSWORD')
salt_hex = os.urandom(16).hex()
key = hashlib.scrypt(
    pw.encode(),
    salt=salt_hex.encode(),  # hex string as UTF-8 bytes, NOT raw bytes
    n=16384, r=16, p=1, dklen=64,
    maxmem=128*16384*16*2
)
hash_value = f'{salt_hex}:{key.hex()}'

# Then UPDATE account SET password = '<hash>' WHERE account_id = '...';
```

## Systemd Services

```ini
# /etc/systemd/system/paperclip.service
[Unit]
Description=Paperclip Control Plane
After=network.target postgresql.service
Requires=postgresql.service

[Service]
Type=simple
User=root
WorkingDirectory=/opt/paperclip
Environment=NODE_ENV=development
Environment=PATH=/usr/local/bin:/root/.local/bin:/usr/bin:/bin:/root/.hermes/node/bin
ExecStart=/usr/local/bin/pnpm paperclipai run
Restart=always
RestartSec=5
StandardOutput=append:/var/log/paperclip.log
StandardError=append:/var/log/paperclip.log

[Install]
WantedBy=multi-user.target
```

## Quick Reference

| Task | Command |
|------|---------|
| Paperclip health | `curl http://localhost:3100/api/health` |
| Reset admin password | `python3 -c "..."` + `psql UPDATE account` |
| Bootstrap invite | `pnpm paperclipai auth bootstrap-ceo --force` |
| Check agent sessions | `SELECT session_id FROM agent_runtime_state;` |
| Clear corrupted session | `UPDATE agent_runtime_state SET session_id = NULL WHERE session_id = 'from';` |
| Gateway restart | `hermes gateway restart` |
| Paperclip logs | `journalctl -u paperclip -f` |

## Repository

- **Paperclip**: [paperclipai/paperclip](https://github.com/paperclipai/paperclip) (MIT)
- **Hermes Agent**: [nousresearch/hermes-agent](https://github.com/nousresearch/hermes-agent)

## Company

**Nexoshape** — Santiago, Chile 3D printing startup (nexoshape.com)

Services: prototypes, custom parts, creative prints. Materials: PLA, PETG, PETG-CF, ABS, ASA-CF, PA-CF, PC.

---

> *"The master workflow: Paperclip thinks, Hermes acts, Nexoshape grows."*
