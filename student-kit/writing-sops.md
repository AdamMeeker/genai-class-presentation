# Writing SOPs for AI Agents

SOPs (Standard Operating Procedures) are how you teach agents to do complex tasks reliably.

## SOP Structure
```yaml
---
name: Morning Briefing
version: 1.0.0
description: Generate and deliver a morning briefing
trigger: Cron — 7:15 AM daily
model: sonnet
---
```

## Anatomy of a Good SOP

### 1. Clear Trigger
When does this run? Manual, scheduled, or event-driven?

### 2. Defined Steps
```markdown
## Steps
1. Gather calendar events (next 24 hours)
2. Check weather forecast
3. Scan unread emails for urgency
4. Generate briefing script
5. Synthesize audio with TTS
6. Deliver to speaker + send text summary
```

### 3. Eval Criteria
How do you know it worked?
```markdown
## Eval Criteria
- [ ] Calendar events are accurate
- [ ] Weather is current (not cached from yesterday)
- [ ] Audio plays without errors
- [ ] Delivered before 7:30 AM
```

## Example SOPs You Can Build

| SOP | Trigger | What It Does |
|-----|---------|-------------|
| Morning Briefing | 7:15 AM daily | Calendar + weather + email → audio briefing |
| Email Triage | Every 2 hours | Classify, prioritize, extract action items |
| Weekly Reflection | Friday 7 PM | Review week's decisions, update lessons learned |
| Research Report | Manual | Web search → synthesize → save to notes |
| Git Publish | Before push | Security scan → commit → push with review |

## Key Insight
> "The only part that stays human is coming up with the ideas. Everything else is SOP execution."

Once you define a workflow as an SOP, agents can execute it forever — consistently, without forgetting steps, at 3 AM if needed.

## Security SOP Example
```markdown
## Pre-Push Security Check
1. Scan staged files for API keys, tokens, passwords
2. Check for files > 50MB
3. Verify .gitignore covers secrets
4. If any issues found → BLOCK the push
5. Log what was caught for review
```

This runs automatically via a git pre-push hook. You define it once, it protects you forever.
