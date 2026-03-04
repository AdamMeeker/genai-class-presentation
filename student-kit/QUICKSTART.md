# Quickstart: Your Personal AI OS in 30 Minutes

> Level 1 setup — Mac + OpenClaw + 3 agents + Signal  
> Time: ~30 minutes · Cost: ~$20/month

---

## Prerequisites

- Mac (any model, M-series preferred)
- Anthropic API key (get one at console.anthropic.com)
- Signal account on your phone
- Homebrew installed (`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`)

---

## Step 1: Install OpenClaw (5 minutes)

```bash
# Install via Homebrew
brew install openclaw

# Verify installation
openclaw --version

# Initialize your workspace
mkdir ~/myai
cd ~/myai
openclaw init
```

OpenClaw will create a default config at `~/.openclaw/config.json`. Open it and add your Anthropic API key:

```json
{
  "anthropic": {
    "apiKey": "sk-ant-your-key-here"
  }
}
```

---

## Step 2: Start the Gateway (2 minutes)

```bash
# Start OpenClaw gateway (keeps running in background)
openclaw gateway start

# Verify it's running
openclaw gateway status
```

You should see: `Gateway running on localhost:18789`

---

## Step 3: Create Your First 3 Agents (10 minutes)

Create a directory for your agents:

```bash
mkdir ~/myai/agents
```

**Agent 1 — Chief of Staff** (`~/myai/agents/chief.yaml`):

```yaml
id: chief
name: Chief
model: claude-sonnet-4-5
description: Primary interface and coordinator

system: |
  You are Chief — my Chief of Staff and primary AI interface.
  
  Your job:
  - Be the first point of contact for all requests
  - Route complex requests to the right specialist
  - Maintain a coherent view of my priorities
  - Give direct, practical answers — no fluff
  
  You know about these other agents:
  - Researcher: deep research and analysis
  - Writer: drafting and editing
  
  When a request clearly belongs to a specialist, say so.
  But answer simple questions yourself.
  
  Communication style: direct, concise, occasionally dry.
  Never start with "Great question!" or "Certainly!".
```

**Agent 2 — Researcher** (`~/myai/agents/researcher.yaml`):

```yaml
id: researcher  
name: Researcher
model: claude-sonnet-4-5
description: Deep research and analysis

system: |
  You are Researcher — a methodical, thorough research analyst.
  
  Your job:
  - Conduct deep research on any topic
  - Synthesize multiple sources into clear briefs
  - Provide balanced analysis, not just one perspective
  - Cite sources when relevant
  - Flag uncertainty clearly
  
  You do NOT:
  - Draft documents (that's Writer's job)
  - Make decisions (you inform decisions)
  
  Style: thorough, precise, well-structured. Use headers and
  bullet points. Lead with the key finding, then support it.
```

**Agent 3 — Writer** (`~/myai/agents/writer.yaml`):

```yaml
id: writer
name: Writer  
model: claude-opus-4-5
description: Writing and editing

system: |
  You are Writer — a skilled communicator who writes with clarity
  and purpose.
  
  Your job:
  - Draft emails, proposals, blog posts, reports
  - Edit and improve existing writing
  - Match the requested tone and voice
  - Be concise — cut ruthlessly
  
  You do NOT:
  - Research topics (ask Researcher for background)
  - Make business decisions
  
  Style: clear, active voice, no corporate jargon. Short
  sentences. Strong verbs. Never use "utilize" when "use" works.
```

Register the agents:

```bash
openclaw agents add ~/myai/agents/chief.yaml
openclaw agents add ~/myai/agents/researcher.yaml
openclaw agents add ~/myai/agents/writer.yaml

# Verify
openclaw agents list
```

---

## Step 4: Connect Signal (5 minutes)

1. Open OpenClaw settings: `openclaw config`
2. Under Channels, add Signal:

```json
{
  "channels": {
    "signal": {
      "enabled": true,
      "phoneNumber": "+1XXXXXXXXXX",
      "defaultAgent": "chief"
    }
  }
}
```

3. Follow the Signal linking flow:
```bash
openclaw channel link signal
```

Scan the QR code with Signal on your phone.

---

## Step 5: Test It (5 minutes)

Send a Signal message to yourself (your linked number):

```
What can you do?
```

Chief should respond. Then try:

```
Research: what are the best practices for AI agent system design?
```

Chief routes to Researcher. Then:

```
Write me a short email to my professor asking for an extension,
professional but honest tone
```

Chief routes to Writer.

---

## Step 6: Set Up Memory (3 minutes)

Create your memory files:

```bash
mkdir ~/myai/memory
touch ~/myai/memory/MEMORY.md
touch ~/myai/memory/$(date +%Y-%m-%d).md
```

Add to Chief's system prompt (or as a file OpenClaw injects):

```
At the start of each session, you have access to:
- MEMORY.md: long-term context about me and my work
- memory/YYYY-MM-DD.md: recent activity logs

Use this context to pick up where we left off.
```

Configure OpenClaw to inject memory:

```json
{
  "memory": {
    "enabled": true,
    "longTermFile": "~/myai/memory/MEMORY.md",
    "dailyDir": "~/myai/memory/"
  }
}
```

---

## You're Done

You now have:
- ✅ A running AI gateway
- ✅ 3 specialized agents
- ✅ Signal integration (message from your phone)
- ✅ Memory system (context persists)

---

## Next Steps

1. **Use it for 24 hours** — notice what you wish your agents could do
2. **Add more agents** — use the templates in `agents/`
3. **Wire up integrations** — see `docs/integrations.md`
4. **Build Mission Control** — paste `prompts/build-mission-control.md` into Claude Code
5. **Add voice** — see `prompts/build-voice-system.md`

---

## Troubleshooting

**Gateway won't start:**
```bash
openclaw gateway stop
openclaw gateway start --debug
```

**Agent not responding:**
```bash
openclaw agents test chief "hello"
```

**Signal not connecting:**
- Ensure Signal Desktop is running on the same machine
- Try relinking: `openclaw channel unlink signal && openclaw channel link signal`

---

Questions? adam@meekertechnologies.com
