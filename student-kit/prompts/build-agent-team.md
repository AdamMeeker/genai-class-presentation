# Prompt: Build Your Agent Team

> Copy-paste this into Claude Code (or Claude directly) to design and
> configure your personal agent team.  
> Customize the sections marked with [BRACKETS] before pasting.

---

## Part 1: Design Prompt (use with Claude, not Claude Code)

Use this to think through your agent architecture before building:

```
I'm building a personal AI agent system using OpenClaw. Help me design
my agent team.

## About Me
[DESCRIBE YOURSELF — your work, your goals, your pain points]
Example: "I'm an MSBA student, part-time research assistant, and I also
do freelance data analysis. I spend too much time on email and not enough
on deep work."

## My Current Pain Points
[LIST 5-10 things that eat your time or fall through the cracks]
Example:
- Email takes 2+ hours/day
- I lose track of research threads
- Writing takes forever because I overthink it
- I forget to follow up with people
- Scheduling takes too much back-and-forth

## What I Want Automated
[LIST the specific workflows you'd like to automate]
Example:
- Morning briefing with schedule + priority tasks
- Email triage and digest
- Research synthesis for papers
- First drafts of professional emails

## My Constraints
- Budget for Claude API: [$/month]
- Technical comfort: [beginner / intermediate / advanced]
- Time to set up: [weekend / 1 week / ongoing project]

---

Based on this, help me:
1. Design a 3-5 agent starting team (names, roles, model tier)
2. Write system prompts for each
3. Define routing logic (which queries go to which agent)
4. Identify one cron job to start with
5. Suggest what to add in phase 2

Format each agent as:

## [Agent Name] — [Role]
Model: [haiku / sonnet / opus]
Domain: [what they handle]
System Prompt: [full prompt]
Routing trigger: [what keywords/contexts route here]
```

---

## Part 2: Implementation Prompt (use with Claude Code)

After you have your agent designs, use this to implement them:

```
I'm setting up my OpenClaw agent system. Help me create the configuration
files for my agent team.

## OpenClaw Setup
OpenClaw is installed and running at localhost:18789.
My workspace is at [YOUR_WORKSPACE_DIR, e.g., ~/myai/].

## My Agents

[PASTE YOUR DESIGNED AGENTS HERE]

Example format:
### Barack
- Model: claude-sonnet-4-6
- Role: Chief of Staff, primary interface
- System prompt: [paste the full prompt]
- Voice: obama (Chatterbox TTS at localhost:4126)

### Researcher
- Model: claude-sonnet-4-5
- Role: Deep research and analysis
- System prompt: [paste the full prompt]
- Voice: text-only

[... continue for all agents]

## Tasks

1. Create YAML config files for each agent in ~/myai/agents/
2. Register each agent with OpenClaw: `openclaw agents add <file>`
3. Create a basic routing config that routes to Chief by default,
   with keyword-based routing for specialists
4. Set up memory injection from ~/myai/memory/ for the Chief agent
5. Create a cron job config for my morning briefing at 7 AM

## Morning Briefing Config
The morning briefing should:
- Run at 7:00 AM weekdays
- Pull from: [YOUR DATA SOURCES — calendar, tasks, etc.]
- Go to my Signal number: [YOUR NUMBER]
- Agent: Barack (chief)
- Speak via Obama voice (TTS)

## Routing Rules
[DESCRIBE YOUR ROUTING LOGIC]
Example:
- Default → Barack
- "research:" prefix → Researcher
- "write:" prefix → Writer
- "legal:" prefix → Legal
- "code:" prefix → Dev

## File Structure Expected
```
~/myai/
├── agents/
│   ├── barack.yaml
│   ├── researcher.yaml
│   └── ...
├── config/
│   ├── routing.yaml
│   └── crons.yaml
└── memory/
    └── MEMORY.md (create empty)
```

After creating files, test with:
`openclaw agents test barack "What's my agenda today?"`

Show me the output and we'll iterate.
```

---

## Tips

### Agent Naming
Names matter. LLMs have priors:
- `Barack` → measured, strategic, synthesis-oriented
- `Olivia` → direct, accountability-focused, no excuses
- `Matlock` → careful, thorough, experienced

You can use made-up names too — just give the agent a persona to match.

### Starting Small
Don't build 14 agents on day one. Start with 3:
1. Chief (everything routes here first)
2. Researcher (when you need depth)
3. Writer (when output quality matters)

Add agents when you notice the Chief handling the same domain repeatedly.

### Routing Philosophy
Simple routing rules beat complex ones:
- Start with: "Chief gets everything"
- Add: "If message starts with [tag], route to [specialist]"
- Graduate to: semantic routing (Chief decides) once you trust the system

### System Prompt Testing
After writing a system prompt, test it with adversarial queries:
- Ask the agent to do something outside its domain
- Ask it a genuinely hard question in its domain
- Ask it something ambiguous

A well-designed agent should: handle in-domain confidently, redirect
out-of-domain cleanly, and ask for clarification on ambiguous cases.
