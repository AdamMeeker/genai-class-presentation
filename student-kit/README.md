# 🤖 Personal AI Operating System — Student Starter Kit

> **University of Iowa · BAIS 4150 Capstone · Generative AI · Spring 2026**  
> By Adam Meeker, Meeker Technologies  
> [adammeeker.com/blog](https://www.adammeeker.com/blog) · adam@meekertechnologies.com

---

## 📋 Table of Contents

- [What Is This?](#what-is-this)
- [Architecture Overview](#architecture-overview)
- [Quick Start (15 Minutes)](#quick-start-15-minutes)
- [The Three Levels](#the-three-levels)
- [Core Components](#core-components)
  - [OpenClaw — The Gateway](#openclaw--the-gateway)
  - [Claude Code — The Builder](#claude-code--the-builder)
  - [Agents — Your AI Team](#agents--your-ai-team)
  - [Mission Control — The Dashboard](#mission-control--the-dashboard)
  - [Voice System — Talk, Don't Type](#voice-system--talk-dont-type)
  - [SOPs — Repeatable Workflows](#sops--repeatable-workflows)
- [What We Built (Live Demo Features)](#what-we-built-live-demo-features)
- [What's In This Kit](#whats-in-this-kit)
- [Hardware Recommendations](#hardware-recommendations)
- [Cost Breakdown](#cost-breakdown)
- [Useful Links](#useful-links)
- [FAQ](#faq)

---

## What Is This?

This is a complete starter kit for building your own **Personal AI Operating System** — a network of specialized AI agents that run continuously, integrate with your tools, and handle the overhead of your life so you can focus on what matters.

**This isn't a chatbot setup guide. This is infrastructure.**

Think of it as building your own J.A.R.V.I.S. — a system that:
- 🗣️ **Talks to you** via Signal, iMessage, or a web dashboard
- 🎙️ **Speaks to you** with cloned voices on your smart speakers
- 📊 **Manages your data** — CRM, calendar, email, research, finances
- 🤖 **Runs autonomously** — morning briefings, email triage, market analysis
- 🧠 **Remembers everything** — persistent memory across sessions

---

## Architecture Overview

```
┌──────────────────────────────────────────────────────────────┐
│                          YOU                                  │
│       Signal · iMessage · Web Dashboard · Voice               │
└─────────────────────────┬────────────────────────────────────┘
                          │
┌─────────────────────────▼────────────────────────────────────┐
│                  GATEWAY: OpenClaw                            │
│   Session Mgmt · Model Routing · Cron · TTS · Memory         │
│   Channels: Signal, Telegram, Discord, iMessage               │
└──┬────┬────┬────┬────┬────┬────┬────┬────┬────┬─────────────┘
   │    │    │    │    │    │    │    │    │    │
 ┌─▼─┐┌─▼─┐┌─▼─┐┌─▼─┐┌─▼─┐┌─▼─┐┌─▼─┐┌─▼─┐┌─▼─┐┌─▼──┐
 │🎖️ ││🔍 ││✍️ ││⚖️ ││📈 ││🎓 ││🏗️ ││📧 ││🎤 ││... │
 │Chief│Res.│Writ│Leg.│Trad│Sage│Forg│Inbx│Tayl│More│
 └─┬──┘└─┬─┘└─┬─┘└─┬─┘└─┬─┘└─┬─┘└─┬─┘└─┬─┘└─┬─┘└─┬──┘
   │     │    │    │    │    │    │    │    │    │
┌──▼─────▼────▼────▼────▼────▼────▼────▼────▼────▼─────────────┐
│                  INTEGRATION LAYER                            │
│ Calendar · Email · Planner · Home Assistant · Invoice Ninja   │
│ DocuSeal · Sonos · Proxmox · Canvas LMS · OneDrive           │
└──────────────────────────────────────────────────────────────┘
```

---

## Quick Start (15 Minutes)

### Prerequisites
- **macOS** (M-series recommended) or **Linux**
- **Node.js 20+** (`brew install node` or [nodejs.org](https://nodejs.org))
- **Anthropic API key** → [console.anthropic.com](https://console.anthropic.com)

### Step 1: Install OpenClaw
```bash
npm install -g openclaw
```

OpenClaw is the gateway — it manages your AI agents, channels, memory, and scheduled tasks.

📖 [OpenClaw Documentation](https://docs.openclaw.ai) · [GitHub](https://github.com/openclaw/openclaw)

### Step 2: Install Claude Code
```bash
npm install -g @anthropic-ai/claude-code
```

Claude Code is your coding agent — it can build entire applications from a prompt. We'll use it to build your dashboard.

📖 [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)

### Step 3: Initialize OpenClaw
```bash
openclaw init
```

This creates your workspace at `~/.openclaw/` with:
- `openclaw.json` — main configuration
- `SOUL.md` — your AI's personality
- `MEMORY.md` — persistent memory
- `USER.md` — about you

### Step 4: Configure Your API Key
```bash
openclaw config set anthropic.apiKey sk-ant-...
```

### Step 5: Start the Gateway
```bash
openclaw gateway start
```

Your AI is now running! Talk to it via the web interface at `http://localhost:18789` or connect a messaging channel.

### Step 6: Build Your Dashboard (Optional)
```bash
# Create a new project
mkdir ~/mission-control && cd ~/mission-control

# Use Claude Code to build it
claude "Build a Next.js dashboard with dark theme that connects to 
OpenClaw at localhost:18789. Include: home dashboard with status cards,
agent roster page, calendar view, and a chat interface."
```

---

## The Three Levels

### 🟦 Level 1 — The Foundation (1 Weekend)

| Component | Tool | Cost |
|-----------|------|------|
| Gateway | OpenClaw | Free (open source) |
| AI Model | Anthropic Claude | ~$20/month API |
| Channel | Signal or iMessage | Free |
| Agents | 3 personas | Included in config |
| Memory | Markdown files | Free |

**What you get:** An always-on AI assistant that remembers context, runs scheduled tasks, and talks to you on your phone.

### 🟣 Level 2 — The Command Center (2 Weeks)

Everything in Level 1, plus:

| Component | Tool | Cost |
|-----------|------|------|
| Dashboard | Next.js (Claude Code builds it) | Free |
| Voice | Chatterbox TTS (open source) | Free |
| More Agents | 5-8 specialized personas | Included |
| Email | Apple Mail or Outlook integration | Free |
| Calendar | Apple Calendar or M365 | Free |

**What you get:** A web dashboard, voice notifications on speakers, email triage, and calendar awareness.

### 🟡 Level 3 — Full Stack (1-2 Months)

Everything in Level 2, plus:

| Component | Tool | Cost |
|-----------|------|------|
| Servers | Proxmox VE (home lab) | ~$200-2,000 hardware |
| Automation | n8n workflows | Free (self-hosted) |
| Smart Home | Home Assistant | Free (self-hosted) |
| Business | Invoice Ninja, DocuSeal | Free (self-hosted) |
| CRM | Custom graph database | Built with Claude Code |
| Search | QMD hybrid search | Free (open source) |
| Trading | Alpaca API | Free (paper trading) |

**What you get:** Full infrastructure — 14+ agents, automated workflows, business tools, home automation, research platform.

---

## Core Components

### OpenClaw — The Gateway

OpenClaw manages everything: sessions, model routing, scheduling, TTS, and channel connections.

```yaml
# Key features:
- Multi-channel: Signal, Telegram, Discord, iMessage, web
- Model routing: Route different agents to different models
- Cron jobs: Schedule recurring AI tasks
- TTS: Text-to-speech with voice cloning
- Memory: Persistent markdown-based memory system
- Skills: Extensible skill system (clawhub.com)
```

**Key commands:**
```bash
openclaw status          # Check system status
openclaw gateway start   # Start the gateway
openclaw gateway stop    # Stop the gateway
openclaw config set ...  # Update configuration
```

### Claude Code — The Builder

Claude Code is an AI coding agent that lives in your terminal. Give it a prompt, and it builds.

```bash
# Build a complete app
claude "Build a personal CRM with SQLite backend and dark-themed UI"

# Fix a bug
claude "The login page returns 401 even with correct credentials. Debug."

# Add a feature
claude "Add drag-and-drop to the kanban board on the pipeline page"
```

**Why it matters:** You don't need to be a developer. Claude Code writes, tests, and iterates on code. You describe what you want.

### Agents — Your AI Team

Each agent has a **persona**, **model preference**, and **domain**:

| Agent | Role | Emoji | Model | Voice |
|-------|------|-------|-------|-------|
| Barack | Chief of Staff | 🎙️ | Sonnet 4.6 | obama |
| Olivia | Exec Enforcer / Security | 🔒 | Sonnet 4.5 | olivia |
| Marcus | Research & Analytics | 📊 | Sonnet 4.5 | rishi (🇮🇳) |
| Riley | Communications | 💬 | Opus 4.6 | karen (🇦🇺) |
| Matlock | Legal Counsel | ⚖️ | Opus 4.6 | daniel_gb (🇬🇧) |
| Sterling | Finance & Business Ops | 💰 | Sonnet 4.5 | moira (🇮🇪) |
| Vega | Investments & Trading | 📈 | Sonnet 4.5 | alex |
| Sage | Wellness & Priorities | 🌿 | Sonnet 4.5 | lekha (🇮🇳) |
| Forge | Infrastructure | 🔧 | Sonnet 4.5 | nicky |
| Maxwell | PR & Operations | ⚙️ | Sonnet 4.5 | samantha |
| Quinn | Creative | 🎨 | Sonnet 4.5 | fiona (🏴) |
| Taylor | Personal & Family | 👨‍👩‍👧‍👦 | Haiku 4.5 | taylor |
| Inbox | Email Triage | 📥 | Haiku 4.5 | yuna (🇰🇷) |
| Dev | Software Engineer | 💻 | Sonnet 4.6 | tessa (🇿🇦) |
| Herky | Hawkeye Sports | 🦅 | Haiku 4.5 | obama |
| Dewey | Knowledge Curator | 📚 | Sonnet 4.6 | dewey |

**Agent templates** are in the `agents/` folder. Copy `template.md` and customize.

### Mission Control — The Dashboard

A Next.js web application that serves as the command center for your AI system.

**Pages we built:**
- 📊 **Dashboard** — System health, agent status, quick actions
- 👥 **People / CRM** — Contact management with graph visualization
- 💰 **Pipeline** — Commercial leads and opportunities (Kanban)
- 🔬 **Grants** — Research funding intelligence
- 📧 **Email** — AI-powered email management
- 📈 **Trading** — Portfolio tracking and market analysis
- 🎓 **Teaching** — Course management and grading tools
- 📋 **SOPs** — Workflow definitions with evaluations
- 🤖 **Agents** — Team roster and management
- 🔍 **Search** — Hybrid search across all content
- 🏗️ **CMDB** — Infrastructure inventory
- 📊 **AI Usage** — Token usage and cost tracking
- 🏠 **Home** — Smart home control
- 📅 **Calendar** — Schedule and events

### Voice System — Talk, Don't Type

**Chatterbox** is an open-source voice cloning system. Give it a 10-second audio sample, and it clones the voice.

```bash
# Our voice lineup:
# 🎤 Obama — Strategy and big-picture briefings
# 🎵 Taylor Swift — Personal and family updates
# 🔫 Olivia Benson — Work deadlines and accountability
```

Voice is delivered via:
- **Sonos speakers** — Morning briefings, announcements
- **Mission Control** — Live voice notifications in the browser
- **Signal** — Audio message attachments

### SOPs — Repeatable Workflows

Standard Operating Procedures define multi-step workflows:

```yaml
name: Morning Brief
triggers:
  - type: cron
    schedule: "15 7 * * *"  # 7:15 AM daily
steps:
  - Gather calendar events
  - Check email for urgent items
  - Pull weather forecast
  - Generate multi-voice audio briefing
  - Deliver to Sonos + Signal
```

SOPs include **eval sections** — criteria that verify the workflow ran correctly.

---

## What We Built (Live Demo Features)

| Feature | Description | Lines of Code |
|---------|-------------|---------------|
| 🧠 Grant Intelligence | 216 grants, 5-factor scoring, faculty matching | ~800 |
| 💰 Commercial Pipeline | Kanban board, AI-assisted opportunity creation | ~3,300 |
| 🔍 Super Search | BM25 + vector + rerank across 1,586 files | ~400 |
| 🎙️ Voice Notifications | SSE streaming to dashboard, multi-voice TTS | ~530 |
| 📈 Trading Intelligence | 3x daily scans, Polymarket/Kalshi, reflection | ~600 |
| 🏫 Faculty Network | 672 researchers, Semantic Scholar enrichment | ~1,200 |
| 📧 Email Management | AI triage, unsubscribe tracking, categorization | ~900 |
| 🏗️ CMDB | Azure resource inventory and monitoring | ~500 |
| 👤 People Detail | 2-column profile, enrichment, opportunity creation | ~1,300 |

**Total: 40+ pages, 16 agents, 12 SOPs, ~$67 API cost for ~$5,500 equivalent value (82x ROI)**

---

## What's In This Kit

```
student-kit/
├── 📖 README.md              ← You are here
├── 🚀 QUICKSTART.md          ← 30-minute setup guide
├── 🤖 agents/
│   ├── README.md             ← Agent design philosophy
│   ├── barack.md             ← Chief of Staff template
│   ├── olivia.md             ← Executive Enforcer
│   ├── marcus.md             ← Research agent
│   ├── riley.md              ← Writing agent
│   ├── matlock.md            ← Legal agent
│   ├── sterling.md           ← Business ops
│   ├── vega.md               ← Finance/trading
│   ├── sage.md               ← Teaching assistant
│   ├── forge.md              ← Infrastructure
│   ├── maxwell.md            ← PR/Communications
│   ├── quinn.md              ← Personal/family
│   ├── taylor.md             ← Lifestyle
│   ├── inbox.md              ← Email triage
│   ├── dev.md                ← Software engineering
│   ├── herky.md              ← Hawkeye sports correspondent
│   ├── dewey.md              ← Knowledge curator / memory
│   └── template.md           ← Blank template (copy this!)
├── 💬 prompts/
│   ├── build-mission-control.md
│   ├── build-agent-team.md
│   ├── build-voice-system.md
│   └── build-integrations.md
├── 📚 docs/
│   ├── system-prd.md         ← 🆕 Full PRD — build everything from scratch
│   ├── architecture.md
│   ├── openclaw-setup.md
│   ├── hardware.md
│   └── integrations.md
└── 📝 examples/
    └── morning-briefing.md
```

---

## Hardware Recommendations

### 💻 Minimum (Level 1-2)
- **Any Mac** with M-series chip (M1/M2/M3/M4)
- 16GB RAM minimum, 32GB recommended
- **Cost:** Your existing laptop, ~$20/month API

### 🖥️ Recommended (Level 3)
- **Mac mini M4** — Always-on AI gateway ($599-799)
- **Mini PC** (Intel N100) — Proxmox server ($150-250)
- **External SSD** — Storage for media/backups ($50-100)

### 🏠 Full Home Lab (Level 3+)
- Mac mini M4 + Mini PC cluster (3+ nodes)
- Gaming PC with GPU for local AI models and TTS
- **Total: $1,000-3,000** depending on specs

---

## Cost Breakdown

| Service | Monthly Cost | What You Get |
|---------|-------------|--------------|
| Anthropic API (Claude) | $20-100 | Main AI model |
| OpenClaw | Free | Open source gateway |
| Claude Code | Free* | Coding agent (*included with Max plan) |
| Chatterbox TTS | Free | Open source voice cloning |
| Signal | Free | Messaging channel |
| n8n | Free | Self-hosted automation |
| Home Assistant | Free | Self-hosted smart home |
| **Total (Level 1)** | **~$20/mo** | |
| **Total (Level 2-3)** | **~$50-100/mo** | + hardware one-time |

---

## Useful Links

| Resource | URL |
|----------|-----|
| OpenClaw Docs | [docs.openclaw.ai](https://docs.openclaw.ai) |
| OpenClaw GitHub | [github.com/openclaw/openclaw](https://github.com/openclaw/openclaw) |
| OpenClaw Community | [discord.com/invite/clawd](https://discord.com/invite/clawd) |
| ClawHub Skills | [clawhub.com](https://clawhub.com) |
| Claude Code | [docs.anthropic.com/claude-code](https://docs.anthropic.com/en/docs/claude-code) |
| Anthropic Console | [console.anthropic.com](https://console.anthropic.com) |
| Chatterbox TTS | [github.com/resemble-ai/chatterbox](https://github.com/resemble-ai/chatterbox) |
| n8n Automation | [n8n.io](https://n8n.io) |
| Proxmox VE | [proxmox.com](https://www.proxmox.com) |
| Adam's Blog | [adammeeker.com/blog](https://www.adammeeker.com/blog) |

---

## FAQ

**Q: Do I need to know how to code?**  
A: No! Claude Code writes the code for you. You describe what you want in plain English. But understanding basic concepts (APIs, databases, terminal) helps.

**Q: Can I use this on Windows?**  
A: OpenClaw runs on macOS and Linux natively. Windows users can use WSL2 (Windows Subsystem for Linux) — it works great.

**Q: How much does the API cost?**  
A: Typical usage is $20-50/month on Anthropic's API. Heavy usage (lots of agents, frequent cron jobs) can be $100+. Claude Max subscription ($100/month) gives unlimited Claude Code.

**Q: Can I use GPT-4 instead of Claude?**  
A: OpenClaw supports multiple providers. You can route different agents to different models — Claude for complex tasks, GPT-4 for others, Haiku for fast/cheap tasks.

**Q: Is my data private?**  
A: Everything runs locally on your machine. Your files never leave your computer. API calls go to Anthropic (or OpenAI), but they don't train on API data. For maximum privacy, you can run local models via Ollama.

**Q: How long does the full setup take?**  
A: Level 1 (foundation): 1-2 hours. Level 2 (dashboard + voice): a weekend. Level 3 (full stack): 2-4 weeks of iterative building.

---

## Key Principle

> *"The magic you're looking for is in the work you're avoiding."*

Start with Level 1 this weekend. Don't wait until you have the perfect plan. Build, iterate, learn.

---

**Built with** 🤖 OpenClaw + Claude Code + a lot of coffee  
**Questions?** adam@meekertechnologies.com
