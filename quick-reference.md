# Personal AI Infrastructure - Quick Reference

One-page cheat sheet for building with OpenClaw.

## Installation

```bash
# Install OpenClaw
npm install -g openclaw

# Initialize workspace
mkdir ~/clawd && cd ~/clawd
openclaw init

# Set API key (Mac/Linux)
export ANTHROPIC_API_KEY="your-key"

# Start gateway
openclaw gateway start

# Access web dashboard
open http://localhost:18789
```

## Essential Commands

```bash
# Gateway management
openclaw gateway status
openclaw gateway start
openclaw gateway stop
openclaw gateway restart

# Skills management
openclaw skills list
openclaw skills install <skill-name>
openclaw skills update

# Session management
openclaw sessions list
openclaw sessions cleanup --older-than 7d

# Configuration
openclaw configure
openclaw gateway validate
```

## File Structure

```
~/clawd/
├── config.yaml         # Main configuration
├── AGENTS.md          # Agent behavior rules
├── SOUL.md            # Personality definition
├── USER.md            # Info about you
├── TOOLS.md           # Tool documentation
├── MEMORY.md          # Long-term memory
├── HEARTBEAT.md       # Periodic checks
├── memory/            # Daily memory files
│   └── YYYY-MM-DD.md
└── skills/            # Custom skills
    └── my-skill/
        ├── SKILL.md
        └── scripts/
```

## Configuration Snippets

**Minimal config.yaml:**
```yaml
models:
  default: anthropic/claude-sonnet-4-5

providers:
  anthropic:
    apiKey: ${ANTHROPIC_API_KEY}

channels:
  web:
    enabled: true
    port: 18789
```

**With Signal:**
```yaml
channels:
  signal:
    enabled: true
    accounts:
      - number: "+1234567890"
        name: "my-signal"
```

**With Ollama (local LLM):**
```yaml
providers:
  ollama:
    baseUrl: http://localhost:11434

models:
  classification: ollama/llama3.2
  default: anthropic/claude-sonnet-4-5
```

## Creating a Custom Skill

```bash
# Create skill directory
mkdir -p ~/.openclaw/skills/my-skill/scripts

# Create SKILL.md
cat > ~/.openclaw/skills/my-skill/SKILL.md << 'EOF'
# My Skill

Description of what this skill does.

## Usage
User says: "do the thing"

## Tools
Script: `scripts/do-thing.sh`
EOF

# Create script
cat > ~/.openclaw/skills/my-skill/scripts/do-thing.sh << 'EOF'
#!/bin/bash
echo "Doing the thing!"
EOF

# Make executable
chmod +x ~/.openclaw/skills/my-skill/scripts/do-thing.sh

# Document in TOOLS.md
echo "## My Skill\n\`~/.openclaw/skills/my-skill/scripts/do-thing.sh\`" >> ~/clawd/TOOLS.md
```

## Common Integrations

**Weather (wttr.in):**
```bash
curl -s "wttr.in/Iowa City?format=%l:+%c+%t+%w"
```

**Google Calendar (gcalcli):**
```bash
pip install gcalcli
gcalcli agenda
```

**GitHub (gh CLI):**
```bash
brew install gh
gh auth login
gh issue list
```

**Ollama (local LLM):**
```bash
brew install ollama
ollama pull llama3.2
ollama run llama3.2
```

## n8n Workflow Automation

```bash
# Install with Docker
docker run -d \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n

# Access
open http://localhost:5678
```

## Security Checklist

- [ ] API keys in environment variables (not committed)
- [ ] Spending limits set in Anthropic console
- [ ] Review automated actions before full automation
- [ ] Backup configs to private git repository
- [ ] Use Tailscale for remote access (not port forwarding)
- [ ] Monitor logs for suspicious activity
- [ ] Rotate API keys periodically

## Cost Optimization

**Use local Ollama for:**
- Email classification
- Simple Q&A
- Repetitive tasks
- Bulk processing

**Use Claude API for:**
- Complex reasoning
- High-quality generation
- Important decisions
- User-facing responses

**Typical costs:**
- Minimal setup: $5-20/month
- Active use: $20-50/month
- Heavy automation: $50-100/month

## Troubleshooting

**Gateway won't start:**
```bash
# Check logs
tail -f ~/.openclaw/logs/gateway.log

# Kill conflicting process
lsof -ti:18789 | xargs kill -9

# Validate config
openclaw gateway validate
```

**Skill not working:**
```bash
# Check script permissions
chmod +x ~/.openclaw/skills/skill-name/scripts/*.sh

# Test script directly
~/.openclaw/skills/skill-name/scripts/script-name.sh
```

**High API costs:**
```bash
# Check usage
openclaw session status

# Switch to cheaper model
# Edit config.yaml: default: anthropic/claude-haiku-4-5

# Use Ollama for classification
# See "Configuration Snippets" above
```

## Resources

**Documentation:**
- OpenClaw: https://docs.openclaw.ai
- Adam's Blog: https://adammeeker.com/blog

**Community:**
- Discord: https://discord.com/invite/clawd
- GitHub: https://github.com/openclaw/openclaw

**Tools:**
- Skills Hub: https://clawhub.com
- n8n: https://n8n.io
- Ollama: https://ollama.com
- Fabric: https://github.com/danielmiessler/fabric

**Contact Adam:**
- adam@meekertechnologies.com

---

**Version:** 1.0 | **Updated:** 2026-02-20 | **For:** GENAI Class
