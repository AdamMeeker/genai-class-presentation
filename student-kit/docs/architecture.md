# System Architecture

> Full technical architecture of the Personal AI Operating System

---

## Overview

The system is a layered architecture: hardware at the base, a gateway in the middle, specialized agents above that, and integrations connecting outward to the world.

```
┌─────────────────────────────────────────────────────────────────────┐
│  USER INTERFACES                                                    │
│  Signal · iMessage · Email · Mission Control (web) · Voice          │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────────────┐
│  GATEWAY LAYER: OpenClaw                                            │
│                                                                     │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐           │
│  │ Session  │  │  Agent   │  │   Cron   │  │   TTS    │           │
│  │  Mgmt   │  │  Router  │  │Scheduler │  │  Engine  │           │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘           │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────────────┐
│  AGENT LAYER                                                        │
│                                                                     │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐     │
│  │ Barack  │ │  Olivia │ │ Marcus  │ │  Riley  │ │ Matlock │     │
│  │ (Chief) │ │(Enforce)│ │(Researc)│ │ (Write) │ │ (Legal) │     │
│  └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘     │
│                                                                     │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐     │
│  │Sterling │ │  Vega   │ │  Sage   │ │  Forge  │ │ Maxwell │     │
│  │(BizOps) │ │(Finance)│ │(Teach)  │ │(Infra)  │ │  (PR)   │     │
│  └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘     │
│                                                                     │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐                 │
│  │  Quinn  │ │ Taylor  │ │  Inbox  │ │   Dev   │                 │
│  │(Family) │ │(Sched)  │ │(Email)  │ │ (Code)  │                 │
│  └─────────┘ └─────────┘ └─────────┘ └─────────┘                 │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────────────┐
│  INTEGRATION LAYER                                                  │
│                                                                     │
│  M365/Planner  │  Home Assistant  │  Email  │  Invoice Ninja       │
│  DocuSeal      │  Obsidian        │  n8n    │  SearXNG             │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────────────┐
│  HARDWARE LAYER                                                     │
│                                                                     │
│  Mac mini M4         Proxmox Node 1      Proxmox Node 2            │
│  (primary compute)   (services 1)        (services 2)              │
│  OpenClaw, MC,       HA, n8n,            Plex, Invoice             │
│  Chatterbox TTS      AdGuard, Metabase   Ninja, DocuSeal           │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Gateway: OpenClaw

OpenClaw is the central nervous system. All traffic passes through it.

### Key Responsibilities

**Session Management**
- Each agent maintains its own session context
- Memory injection: daily files + MEMORY.md loaded at session start
- Session history stored locally

**Agent Routing**
- Default agent receives all messages
- Routing rules: keyword-based, semantic, or agent-decided
- Agent-to-agent delegation (Barack can hand off to Marcus)

**Cron Scheduler**
- Runs scheduled tasks autonomously
- Each cron: target agent, schedule (cron syntax), message/task
- Morning briefing, email digest, weekly review, etc.

**Channel Multiplexing**
- Signal, iMessage, Email, Web UI on one gateway
- Each channel can have its own default agent
- TTS output configurable per channel

**TTS Integration**
- ElevenLabs-compatible API (Chatterbox TTS locally)
- Per-agent voice configuration
- Inline `[[tts:voiceId=X]]` directives in responses

---

## Memory Architecture

```
Session Start
    │
    ├── Load MEMORY.md          (long-term curated facts)
    ├── Load memory/YYYY-MM-DD.md  (today's log)
    ├── Load memory/YYYY-MM-DD.md  (yesterday's log)
    └── Inject into agent context

During Session
    │
    └── Agent writes key events to memory/YYYY-MM-DD.md

Heartbeat (periodic)
    │
    ├── Review recent daily files
    ├── Distill important events
    └── Update MEMORY.md (long-term)
```

**Three memory tiers:**
1. **Daily files** (`memory/YYYY-MM-DD.md`): Raw logs, temporary context
2. **MEMORY.md**: Curated long-term facts, decisions, lessons learned
3. **Obsidian vault**: Deep notes, project docs, research, synced via iCloud

---

## Agent Architecture

Each agent is:
```yaml
id: agent-id
name: Display Name
model: claude-[haiku|sonnet|opus]-[version]
voice: voice-id (optional)
description: "One-line for routing"

system: |
  [Full system prompt]
  PERSONA: ...
  DOMAIN: ...
  BOUNDARIES: ...
  ESCALATION: ...
  STYLE: ...
```

### Model Distribution (Adam's system)
```
Opus 4.5:   Riley, Matlock, Maxwell    (3 agents — high-quality output)
Sonnet 4.6: Barack, Vega, Forge, Dev   (4 agents — real-time interactive)
Sonnet 4.5: Olivia, Marcus, Sterling, Sage (4 agents — balanced)
Haiku 4.5:  Quinn, Taylor, Inbox       (3 agents — high-volume, cheap)
```

---

## Integration Patterns

### Pull Integration
Agent requests data when asked:
```
User → Barack → "What tasks are due today?"
Barack → [calls Planner API] → Returns tasks
Barack → Synthesizes response → User
```

### Push Integration (Cron-based)
System proactively delivers data:
```
Cron (7 AM) → Barack → [gets calendar + tasks + weather]
Barack → Synthesizes briefing → Signal message → User
```

### Event-Driven (n8n)
External events trigger agent actions:
```
Email with [TASK] arrives → n8n webhook detects
n8n → POST to OpenClaw/Barack → "Create task: [parsed task]"
Barack → Creates Planner task → Confirms via Signal
```

---

## Security Architecture

### Network Zones
```
INTERNET
    │
    ├── Cloudflare Tunnel (public services only)
    │   ├── clients.yourdomain.com → Invoice Ninja
    │   └── contracts.yourdomain.com → DocuSeal
    │
    └── Tailscale VPN (admin access only)
        ├── n8n (192.168.1.120:5678)
        ├── Home Assistant (192.168.1.124:8123)
        └── Proxmox (192.168.1.55, 192.168.1.70)

LAN (192.168.1.x)
    ├── Mac mini (192.168.1.100) — primary compute
    ├── Proxmox 1 (192.168.1.55)
    └── Proxmox 2 (192.168.1.70)
```

### Data Residency
| Data Type | Stays Local | Cloud |
|-----------|-------------|-------|
| Memory files | ✅ Mac mini | ❌ |
| Voice audio | ✅ Chatterbox local | ❌ |
| Contracts | ✅ Proxmox 2 | ❌ |
| Invoices | ✅ Proxmox 2 | ❌ |
| Agent prompts | ✅ Mac mini | ❌ |
| AI reasoning | — | ✅ Claude API only |
| Messages | ✅ Signal E2E | Signal servers |
| Notes | ✅ iCloud encrypted | iCloud |

---

## Request Flow: Full Example

**"Research the Agilent Q1 earnings report"**

```
1. User sends Signal message
2. OpenClaw receives via Signal channel
3. Default routing → Barack
4. Barack reads message
5. Barack recognizes research request
6. Barack delegates to Marcus ("Research: Agilent Q1 earnings")
7. Marcus executes: web search + synthesis
8. Marcus returns structured brief to Barack
9. Barack synthesizes final response
10. Barack responds via Signal (text + optional Obama TTS)
11. Barack logs key points to memory/YYYY-MM-DD.md
```

Total: ~8-15 seconds
API calls: 2 (Barack + Marcus)
Cost: ~$0.02 (Sonnet 4.6 + Sonnet 4.5)
