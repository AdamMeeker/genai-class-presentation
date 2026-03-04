# OpenClaw Setup Guide

> Complete installation and configuration guide for OpenClaw

---

## What is OpenClaw?

OpenClaw is a personal AI gateway that:
- Manages multiple AI agents with persistent sessions
- Routes messages across channels (Signal, iMessage, Email, Web)
- Runs scheduled cron jobs autonomously
- Integrates with TTS providers for voice output
- Handles memory injection and context management

Website: openclaw.ai  
Docs: openclaw.ai/docs

---

## Installation

### macOS (Homebrew — recommended)

```bash
# Install
brew install openclaw

# Verify
openclaw --version

# Initialize workspace
mkdir ~/myai
cd ~/myai
openclaw init
```

### Manual Install

```bash
# Download latest release
curl -L https://openclaw.ai/install.sh | bash

# Or from GitHub releases
# https://github.com/openclaw/openclaw/releases
```

---

## Directory Structure

After `openclaw init`, your workspace looks like:

```
~/myai/               (your workspace)
├── agents/           (agent config files)
├── memory/           (daily memory logs)
│   └── MEMORY.md     (long-term memory)
├── config/           (routing, cron configs)
└── integrations/     (integration modules)

~/.openclaw/          (global config)
└── config.json       (API keys, settings)
```

---

## Configuration

### Global Config (`~/.openclaw/config.json`)

```json
{
  "anthropic": {
    "apiKey": "sk-ant-your-key-here"
  },
  
  "gateway": {
    "port": 18789,
    "host": "localhost"
  },
  
  "tts": {
    "provider": "chatterbox",
    "baseUrl": "http://localhost:4126",
    "defaultVoice": "default",
    "mode": "tagged"
  },
  
  "memory": {
    "enabled": true,
    "longTermFile": "~/myai/memory/MEMORY.md",
    "dailyDir": "~/myai/memory/",
    "injectIntoDefault": true
  },
  
  "channels": {
    "signal": {
      "enabled": true,
      "phoneNumber": "+1XXXXXXXXXX",
      "defaultAgent": "chief"
    }
  }
}
```

### Anthropic API Key

Get your key at console.anthropic.com → API Keys → Create key

Start with a small budget ($10-20) while testing. Monitor usage at console.anthropic.com.

---

## Agent Configuration

### Agent YAML Format

```yaml
# ~/myai/agents/chief.yaml

id: chief
name: Chief
model: claude-sonnet-4-5
description: "Primary interface and coordinator"
voice: myvoice  # optional; omit for text-only

# Optional metadata
emoji: 🎩
tags: [primary, coordinator]

system: |
  You are Chief — my primary AI interface and coordinator.
  
  [Full system prompt here]
```

### Registering Agents

```bash
# Add a single agent
openclaw agents add ~/myai/agents/chief.yaml

# Add all agents in a directory
openclaw agents add ~/myai/agents/

# List registered agents
openclaw agents list

# Test an agent
openclaw agents test chief "What can you do?"

# Remove an agent
openclaw agents remove chief
```

---

## Gateway Management

```bash
# Start the gateway (runs in background)
openclaw gateway start

# Check status
openclaw gateway status

# View logs
openclaw gateway logs

# Restart
openclaw gateway restart

# Stop
openclaw gateway stop
```

The gateway runs on `localhost:18789` by default and exposes a REST API.

### Key API Endpoints

```bash
# List agents
curl http://localhost:18789/api/agents

# Send message to agent
curl -X POST http://localhost:18789/api/agents/chief/message \
  -H "Content-Type: application/json" \
  -d '{"message": "What time is it?"}'

# Get session history
curl http://localhost:18789/api/sessions/chief/history

# List cron jobs
curl http://localhost:18789/api/crons
```

---

## Channel Setup

### Signal

1. Install Signal on your Mac (signal.org/download)
2. Link your account:
   ```bash
   openclaw channel link signal
   ```
3. Scan the QR code with Signal on your phone
4. Test:
   ```bash
   openclaw channel test signal "Hello from OpenClaw"
   ```

### iMessage (macOS only)

1. Enable iMessage on your Mac (Messages.app → signed in)
2. Configure:
   ```json
   "imessage": {
     "enabled": true,
     "defaultAgent": "quinn"
   }
   ```
3. Test: `openclaw channel test imessage "+1XXXXXXXXXX"`

### Email

Configure your email provider's IMAP settings:
```json
"email": {
  "enabled": true,
  "imap": {
    "host": "imap.gmail.com",
    "port": 993,
    "user": "you@gmail.com",
    "password": "app-specific-password"
  },
  "defaultAgent": "inbox",
  "pollInterval": 300
}
```

---

## Cron Jobs

### Cron Config (`~/myai/config/crons.yaml`)

```yaml
crons:
  - id: morning-briefing
    schedule: "45 6 * * 1-5"  # 6:45 AM weekdays
    agent: chief
    message: |
      Good morning. Please generate my morning briefing:
      - Today's calendar events
      - Top 3 priority tasks
      - Any urgent email flags
      - Weather summary
    channel: signal
    tts: true
    voice: myvoice

  - id: email-triage
    schedule: "*/30 8-18 * * 1-5"  # Every 30 min, business hours
    agent: inbox
    message: "Check and triage unread email. Send digest if 3+ new messages."
    channel: signal
    tts: false

  - id: weekly-review
    schedule: "0 16 * * 5"  # Friday 4 PM
    agent: chief
    message: "Generate my weekly review: what got done, what's pending, priorities for next week"
    channel: signal
```

### Register Crons

```bash
openclaw crons load ~/myai/config/crons.yaml
openclaw crons list
openclaw crons test morning-briefing  # Run now to test
```

---

## Memory System

### Memory Injection

OpenClaw injects memory into agent context automatically when configured:

```json
"memory": {
  "enabled": true,
  "longTermFile": "~/myai/memory/MEMORY.md",
  "dailyDir": "~/myai/memory/",
  "maxTokens": 2000,
  "injectIntoDefault": true
}
```

### Memory File Format

`~/myai/memory/MEMORY.md` — long-term facts:
```markdown
# Long-Term Memory

## About Me
- Name: [Your name]
- Role: MSBA student at University of Iowa
- Key projects: [list]

## Important People
- [Name]: [relationship/context]

## Ongoing Projects
- [Project]: [status/context]

## Preferences
- [preference notes]
```

`~/myai/memory/YYYY-MM-DD.md` — daily logs (auto-created):
```markdown
# 2026-03-03

## Sessions
- 09:15 — Asked Chief about thesis topic options
- 14:30 — Riley drafted email to advisor

## Decisions Made
- Chose topic: AI agent systems in SMB contexts

## Tasks Created
- Research competing frameworks by Friday
```

---

## Routing Configuration

### Simple Routing (`~/myai/config/routing.yaml`)

```yaml
routing:
  default: chief
  
  rules:
    # Keyword prefix routing
    - match: "^research:"
      agent: researcher
      strip_prefix: true
      
    - match: "^write:"
      agent: writer
      strip_prefix: true
      
    - match: "^code:"
      agent: dev
      strip_prefix: true
      
    - match: "^legal:"
      agent: legal
      strip_prefix: true
```

### Semantic Routing (via Chief)

Let Barack/Chief decide what to do with everything — configure routing in his system prompt instead of in routing.yaml. This scales better as your system grows.

---

## Troubleshooting

### Gateway won't start
```bash
openclaw gateway stop  # Ensure nothing is stuck
lsof -i :18789         # Check if port is in use
openclaw gateway start --debug  # Verbose logging
```

### Agent not responding
```bash
openclaw agents test chief "hello"  # Direct test
openclaw gateway logs | tail -20    # Check error logs
openclaw config validate            # Validate config
```

### Signal not receiving messages
```bash
openclaw channel status signal
openclaw channel link signal  # Re-link if needed
```

### API key issues
- Verify key at console.anthropic.com
- Check model name is correct (models update — check Anthropic docs)
- Check rate limits if getting 429 errors

### Memory not injecting
```bash
openclaw memory status
cat ~/myai/memory/MEMORY.md  # Verify file exists and is readable
openclaw config validate
```
