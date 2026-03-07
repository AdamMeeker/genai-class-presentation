# Quickstart: Your Personal AI OS in 30 Minutes

> Level 1 setup — OpenClaw + 3 agents + Signal  
> Time: ~30 minutes · Cost: ~$20/month  
> **Works on:** macOS · Linux · Windows (via WSL2)

---

## Prerequisites

- An AI model provider (see options below — pick one)
- Signal account on your phone
- One of the following:
  - **Mac:** Homebrew installed
  - **Linux:** Ubuntu/Debian 20.04+
  - **Windows:** WSL2 enabled (see Windows Setup below)

---

## Choosing Your AI Provider

OpenClaw supports multiple AI providers. Pick the option that fits your budget and situation:

### Option A — Anthropic API Key (Recommended for production)
**Cost:** Pay-per-use (~$5-20/month typical usage)  
**Get it:** [console.anthropic.com](https://console.anthropic.com) → API Keys  
**Best for:** Predictable billing, production use, no subscription required

```json
{ "anthropic": { "apiKey": "sk-ant-your-key-here" } }
```

### Option B — Claude Pro/Max Subscription (~$20-100/month)
**Cost:** $20/month (Pro) or $100/month (Max)  
**Get it:** [claude.ai](https://claude.ai) → Subscribe, then run `openclaw setup-token`  
**⚠️ Important caveat:** Using a Pro/Max subscription with OpenClaw involves capturing an OAuth token from Claude.ai. This works currently, but Anthropic's terms of service may restrict this use. If it stops working, switch to Option A or Option C.

```bash
# After subscribing at claude.ai:
openclaw setup-token
# Follow the prompts — paste the OAuth token when asked
```

### Option C — GitHub Copilot Pro+ (~$19/month)
**Cost:** ~$19/month, includes Claude Sonnet 4.6 + GPT-4o + Gemini  
**Get it:** [github.com/features/copilot](https://github.com/features/copilot)  
**Best for:** Students who already have GitHub (education discount available — may be free!)

```bash
openclaw configure --provider github-copilot
```

### Option D — OpenRouter (Pay-per-use, many models)
**Cost:** Pay-per-use, often cheaper than direct APIs  
**Get it:** [openrouter.ai](https://openrouter.ai) → API Keys  
**Best for:** Budget-conscious users who want access to many models (Llama, Mistral, Gemini, Claude, etc.)

```bash
openclaw configure --provider openrouter
```

### Option E — Google Gemini API (Free tier available)
**Cost:** Free tier available, then pay-per-use  
**Get it:** [aistudio.google.com](https://aistudio.google.com) → Get API Key  
**Best for:** Zero-cost experimentation

```bash
openclaw configure --provider google
```

### Option F — Local Models via Ollama (Free, runs on your Mac)
**Cost:** Free — runs locally on your hardware  
**Get it:** [ollama.ai](https://ollama.ai) → Install → `ollama pull llama3.2`  
**Best for:** Privacy, offline use, no API costs. Slower than cloud models.

```bash
openclaw configure --provider ollama
```

---

> **Recommendation for students:** Start with **Option C (GitHub Copilot)** if you have a .edu email (free or discounted). Otherwise use **Option A (Anthropic API)** — $5 will last weeks during class.

---

---

## 🍎 Mac Setup

### Step 1: Install OpenClaw (5 minutes)

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

OpenClaw creates a config at `~/.openclaw/config.json`. Add your API key:

```json
{
  "anthropic": {
    "apiKey": "sk-ant-your-key-here"
  }
}
```

---

## 🐧 Linux Setup

### Step 1: Install Node.js 20+

```bash
# Ubuntu/Debian
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# Verify
node --version  # Should show v20+
```

### Step 2: Install OpenClaw

```bash
npm install -g openclaw
openclaw --version
```

### Step 3: Initialize

```bash
mkdir ~/myai && cd ~/myai
openclaw init
```

Add your API key to `~/.openclaw/config.json`:
```json
{
  "anthropic": {
    "apiKey": "sk-ant-your-key-here"
  }
}
```

---

## 🪟 Windows Setup (WSL2)

WSL2 gives you a full Linux environment inside Windows. OpenClaw runs great in it.

### Step 1: Enable WSL2

Open **PowerShell as Administrator** and run:

```powershell
wsl --install
```

Restart your computer when prompted. This installs Ubuntu by default.

> **Already have WSL?** Make sure it's WSL2: `wsl --set-default-version 2`

### Step 2: Open Ubuntu Terminal

After restart, search "Ubuntu" in Start menu and open it. You'll set a username and password on first launch.

### Step 3: Install Node.js in WSL

```bash
# Inside Ubuntu terminal
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

node --version  # Should show v20+
```

### Step 4: Install OpenClaw

```bash
npm install -g openclaw
openclaw --version
```

### Step 5: Initialize your workspace

```bash
mkdir ~/myai && cd ~/myai
openclaw init
```

Configure your API key:

```bash
# Option A: Edit directly
nano ~/.openclaw/config.json
# Add: "anthropic": { "apiKey": "sk-ant-your-key-here" }

# Option B: Use the CLI
openclaw config set anthropic.apiKey sk-ant-your-key-here
```

### Step 6: Set environment variable (optional but recommended)

```bash
# Add to ~/.bashrc so it persists
echo 'export ANTHROPIC_API_KEY="sk-ant-your-key-here"' >> ~/.bashrc
source ~/.bashrc
```

> **Windows tip:** VS Code has a "WSL" extension — install it and you can edit your Linux files with VS Code's full UI. Run `code .` from the WSL terminal.

---

## 🚀 All Platforms: Steps 2-6

---

## Step 2: Start the Gateway (2 minutes)

```bash
# Start OpenClaw gateway (keeps running in background)
openclaw gateway start

# Verify it's running
openclaw gateway status
```

You should see: `Gateway running on localhost:18789`

Open in browser: [http://localhost:18789](http://localhost:18789)

> **Windows users:** The gateway runs inside WSL, but you can access `localhost:18789` from your Windows browser. It just works.

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

1. Install **Signal Desktop** → [signal.org/download](https://signal.org/download) *(Mac/Windows — runs natively)*
2. Open OpenClaw config and add Signal:

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

> **Windows users:** Signal Desktop runs natively on Windows (not in WSL). OpenClaw communicates with it through a local socket that works across the WSL boundary.

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

## You're Done With Installation

You now have:
- ✅ A running AI gateway
- ✅ OpenClaw web UI at localhost:18789
- ✅ Anthropic API connected

---

## Step 7: Run Your Onboarding Interview (15 minutes)

This is the most important step. Don't skip it.

Open the web UI at [http://localhost:18789](http://localhost:18789) and paste this:

```
I just installed OpenClaw. Read the file at student-kit/ONBOARDING.md 
and run me through the onboarding interview. I want to design my 
personal AI system from scratch.
```

**What happens next:**
- Your agent interviews you (15-20 min)
- Asks about your role, tools, pain points, and setup
- Recommends a custom agent team (3-8 agents, not all 16)
- Generates your personalized `SOUL.md`, `USER.md`, and agent configs
- Produces a ready-to-run Claude Code prompt that builds your dashboard
- Sets up your first automation cron job

By the end, Claude Code will be building your Mission Control while you grab lunch.

---

## Next Steps (after onboarding)

1. **Follow the onboarding output** — it'll tell you exactly what to do
2. **Use it for 24 hours** before adding anything else
3. **Add more agents** — 19 templates in `agents/` including Pulse (health coach) and Ralph (autonomous loop agent)
4. **Wire up integrations** — see `docs/integrations.md`
5. **Deep dive** — `docs/system-prd.md` has the full architecture and build prompts

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

**Windows / WSL-specific issues:**

*Can't access localhost:18789 from Windows browser:*
```bash
# Get your WSL IP address
hostname -I
# Try that IP:18789 instead of localhost
```

*Permission denied installing npm packages:*
```bash
# Fix npm global directory permissions
mkdir -p ~/.npm-global
npm config set prefix ~/.npm-global
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
# Then retry: npm install -g openclaw
```

*WSL2 performance is slow:*
- Keep your project files in the Linux filesystem (`~/myai`) not Windows filesystem (`/mnt/c/...`)
- Windows filesystem access through WSL is significantly slower

*Signal Desktop on Windows + OpenClaw in WSL:*
- This combination works but requires OpenClaw 1.4+ for cross-boundary socket support
- If it fails, run OpenClaw on Windows PowerShell instead (same `npm install -g openclaw`)

---

Questions? adam@meekertechnologies.com
