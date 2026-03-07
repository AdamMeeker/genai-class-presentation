# Building a Personal AI Operating System

> Presentation for University of Iowa MSBA Generative AI Course  
> Adam Meeker · Meeker Technologies · 2026

---

## Overview

This presentation demonstrates a complete Personal AI Operating System — a sophisticated network of 18 specialized AI agents running 24/7, integrated with home infrastructure, business tools, and daily workflows.

**Target Audience:** MSBA students — technically capable, ambitious, want to build their own systems.

**Core Thesis:** AI is not a tool. It's infrastructure. And you should run your own.

---

## The System (Quick Facts)

- **18 specialized agents** — each with distinct personas, domains, and voices
- **3-tier hardware** — Mac mini M4 + 2 Proxmox nodes (~$2,000 total)
- **20-page dashboard** — Mission Control (Next.js, dark theme)
- **6 cloned voices** — Chatterbox TTS, MPS-accelerated, local inference
- **Multiple integrations** — M365, Home Assistant, Signal, Email, n8n, Invoice Ninja, DocuSeal
- **Continuous operation** — cron jobs, autonomous email triage, morning briefings
- **Cost:** ~$50-100/month ongoing (API + services)

---

## Files in This Directory

```
genai-class/
├── index.html              ← Main Reveal.js presentation (39 slides)
├── reveal/                 ← Reveal.js library files (do not edit)
├── agent-intros.json       ← Voice intro scripts for all 18 agents
├── student-kit/            ← Complete starter kit for students
│   ├── README.md           ← Overview + architecture diagram
│   ├── QUICKSTART.md       ← 30-minute setup guide
│   ├── agents/             ← All 18 agent templates + design philosophy
│   │   ├── README.md
│   │   ├── barack.md       ← Chief of Staff
│   │   ├── olivia.md       ← Executive Enforcer
│   │   ├── marcus.md       ← Research & Intelligence
│   │   ├── riley.md        ← Communications & Writing
│   │   ├── matlock.md      ← Legal Counsel
│   │   ├── sterling.md     ← Business Operations
│   │   ├── vega.md         ← Quantitative Finance
│   │   ├── sage.md         ← Teaching Assistant
│   │   ├── forge.md        ← Infrastructure Engineer
│   │   ├── maxwell.md      ← PR & Communications
│   │   ├── quinn.md        ← Personal & Family
│   │   ├── taylor.md       ← Lifestyle & Scheduling
│   │   ├── inbox.md        ← Email Triage
│   │   ├── dev.md          ← Software Engineering
│   │   └── template.md     ← Blank template to copy
│   ├── prompts/            ← Copy-paste into Claude Code
│   │   ├── build-mission-control.md
│   │   ├── build-agent-team.md
│   │   ├── build-voice-system.md
│   │   └── build-integrations.md
│   ├── docs/               ← Technical documentation
│   │   ├── architecture.md
│   │   ├── openclaw-setup.md
│   │   ├── hardware.md
│   │   └── integrations.md
│   └── examples/
│       └── morning-briefing.md  ← Full automation walkthrough
└── README.md               ← This file
```

---

## Presenting the Deck

### Opening the Presentation

```bash
# Navigate to directory
cd ~/clawd/presentations/genai-class/

# Open in browser
open index.html

# Or serve via local server (if needed)
python3 -m http.server 8000
# Then open: http://localhost:8000/index.html
```

### Keyboard Controls

- **Arrow keys** — navigate slides
- **F** — fullscreen
- **S** — speaker notes (if added)
- **ESC** — overview mode
- **B** — blank screen (pause)

### Slide Count

39 slides covering:
1. Title + About Adam
2. Problem statement (why one big brain fails)
3. Solution (treat AI as infrastructure)
4. System philosophy
5. Architecture overview
6. Hardware layer
7. Gateway layer (OpenClaw)
8. Agent team (14 agents across 6 domains)
9. Model selection strategy
10. Voice system
11. Mission Control dashboard
12. Key integrations (Planner, Email, Home Assistant, Business tools)
13. Memory system
14. Security & privacy
15. Real results / use cases
16. Cost breakdown
17. How to build your own (3 levels)
18. Student starter kit
19. Claude Code as builder
20. Resources + Q&A

---

## Student Starter Kit

The `student-kit/` directory is a complete, standalone resource that students can use to build their own AI operating system.

**Key principle:** Every prompt file is copy-paste ready for Claude Code. Students can literally build their own system by following the prompts.

### Quick Start Path for Students

1. **Read:** `student-kit/README.md` — understand the architecture
2. **Do:** `student-kit/QUICKSTART.md` — 30-minute setup (Level 1)
3. **Build:** Use prompts in `student-kit/prompts/` to expand (Level 2-3)
4. **Customize:** Adapt agent templates in `student-kit/agents/` for their own domains

---

## Design System

The presentation uses a dark cyberpunk aesthetic:

- **Background:** #0a0a0f (near black)
- **Accent:** #00d9ff (cyan)
- **Secondary:** #a855f7 (purple)
- **Success:** #22c55e (green)
- **Text:** #e2e8f0 (light gray)

Emoji-heavy, visual, professional but not corporate boring.

---

## Agent Voice Intros

`agent-intros.json` contains 2-3 sentence intro scripts for each of the 18 agents, written in character. These can be used for:

- TTS demo during the presentation
- Student understanding of agent personas
- Template for students creating their own agent voices

Example usage:
```bash
# Generate intro audio for Barack
cat agent-intros.json | jq -r '.barack' | \
  curl -X POST http://localhost:4126/v1/text-to-speech/obama \
    -H "Content-Type: application/json" \
    -d @- -o barack-intro.mp3
```

---

## Updating the Presentation

To modify the presentation:

1. Edit `index.html` directly (it's all inline HTML/CSS/JS)
2. Keep the existing `reveal/` directory (library files)
3. Test locally: `open index.html`
4. Commit changes: `git add . && git commit -m "Update presentation"`

The presentation is completely self-contained — no build step, no npm install, just open the HTML file.

---

## GitHub Repository (Future)

This starter kit is intended to be published to GitHub as:
```
github.com/adammeeker/personal-ai-os-starter
```

Students can then:
```bash
git clone https://github.com/adammeeker/personal-ai-os-starter.git
cd personal-ai-os-starter
# Follow QUICKSTART.md
```

---

## Learning Outcomes

By the end of this presentation + hands-on work with the starter kit, students should be able to:

1. **Understand** the architectural difference between "AI chatbot" and "AI infrastructure"
2. **Design** a multi-agent system with specialized roles and clear boundaries
3. **Implement** OpenClaw with 3-5 agents on their own Mac (Level 1)
4. **Integrate** at least one external service (calendar, email, or tasks)
5. **Build** a simple Mission Control dashboard using Claude Code
6. **Evaluate** model selection tradeoffs (Haiku vs. Sonnet vs. Opus)
7. **Deploy** cron-based automations (morning briefing, email digest)
8. **Explain** the privacy and security implications of local-first AI infrastructure

---

## Contact & Support

**Adam Meeker**  
Email: adam@meekertechnologies.com  
Blog: adammeeker.com/blog  
LinkedIn: linkedin.com/in/adammeeker

**For Students:**
- Office hours: [TBD via class schedule]
- Questions: Post in class discussion board or email directly
- Troubleshooting: Check `docs/openclaw-setup.md` troubleshooting section first

---

## Version History

- **2026-03-03:** Complete rebuild — new presentation, full student kit, all 14 agents documented
- **2026-03-06:** Updated to 18 agents — added Hunter (RFP intelligence), Pulse (health coach), Herky (Hawkeye sports), Dewey (knowledge curator)
- **2026-02-XX:** Original version (now outdated)

---

## License

Presentation content: © 2026 Adam Meeker  
Student starter kit: MIT License (free to use, modify, distribute)  
OpenClaw: See openclaw.ai/license  
Claude API: See anthropic.com/terms
