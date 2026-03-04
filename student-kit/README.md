# Personal AI Operating System — Student Starter Kit

> Built for University of Iowa MSBA Generative AI · 2026  
> By Adam Meeker, Meeker Technologies

---

## What Is This?

This is a complete starter kit for building your own **Personal AI Operating System** — a network of specialized AI agents that run continuously, integrate with your tools, and handle the overhead of your life so you can focus on what matters.

This isn't a chatbot setup guide. This is infrastructure.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        YOU                                      │
│         Signal / iMessage / Email / Web Dashboard               │
└──────────────────────────┬──────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────┐
│                   GATEWAY: OpenClaw                             │
│    Session Management · Routing · Cron · TTS · Memory           │
└────┬──────────┬──────────┬──────────┬──────────┬───────────────┘
     │          │          │          │          │
  ┌──▼──┐   ┌──▼──┐   ┌──▼──┐   ┌──▼──┐   ┌──▼──┐
  │Chief│   │ Res │   │Write│   │Legal│   │ ... │
  │Staff│   │earch│   │  er │   │     │   │     │
  └──┬──┘   └──┬──┘   └──┬──┘   └──┬──┘   └──┬──┘
     │          │          │          │          │
┌────▼──────────▼──────────▼──────────▼──────────▼───────────────┐
│                   INTEGRATION LAYER                             │
│   Calendar · Email · Tasks · Home · Invoicing · Contracts       │
└─────────────────────────────────────────────────────────────────┘
```

---

## The Three Levels

### 🟦 Level 1 — The Foundation (Weekend)
- OpenClaw on your Mac
- 3 agents with distinct personas  
- Signal or iMessage channel
- Basic memory files
- **Cost: ~$20/month (API + OpenClaw)**

### 🟣 Level 2 — The Command Center (2 weeks)
- Level 1 + Mission Control dashboard (Claude Code builds it)
- 5-8 agents with voice (Chatterbox TTS)
- Email and calendar integration
- **Cost: Level 1 + free/open source software**

### 🟡 Level 3 — Full Stack (1-2 months)
- Level 2 + Proxmox home servers
- 14+ agents with full integration suite
- n8n workflow automation, Home Assistant, self-hosted business tools
- **Cost: ~$2,000 hardware + ~$50-100/month ongoing**

---

## What's In This Kit

```
student-kit/
├── README.md              ← You are here
├── QUICKSTART.md          ← 30-minute setup guide
├── agents/
│   ├── README.md          ← Agent design philosophy
│   ├── barack.md          ← Chief of Staff template
│   ├── olivia.md          ← Executive Enforcer template
│   ├── marcus.md          ← Research agent template
│   ├── riley.md           ← Writing agent template
│   ├── matlock.md         ← Legal agent template
│   ├── sterling.md        ← Business ops template
│   ├── vega.md            ← Finance/trading template
│   ├── sage.md            ← Teaching assistant template
│   ├── forge.md           ← Infrastructure agent template
│   ├── maxwell.md         ← PR/Communications template
│   ├── quinn.md           ← Personal/family template
│   ├── taylor.md          ← Lifestyle/scheduling template
│   ├── inbox.md           ← Email triage template
│   ├── dev.md             ← Software engineering template
│   └── template.md        ← Blank template to copy
├── prompts/
│   ├── build-mission-control.md    ← Paste into Claude Code
│   ├── build-agent-team.md         ← Paste into Claude Code
│   ├── build-voice-system.md       ← Paste into Claude Code
│   └── build-integrations.md       ← Paste into Claude Code
├── docs/
│   ├── architecture.md    ← Full system design
│   ├── openclaw-setup.md  ← Installation + config
│   ├── hardware.md        ← Hardware recommendations
│   └── integrations.md    ← Integration guides
└── examples/
    └── morning-briefing.md ← A real automation walkthrough
```

---

## Key Principle

> "The magic you're looking for is in the work you're avoiding."

Start with Level 1 this weekend. Don't wait until you have the perfect plan.

---

## Contact

Adam Meeker · adam@meekertechnologies.com · adammeeker.com/blog
