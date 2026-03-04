# Agent Design Philosophy

> "Each agent should have a narrow job and do it well."

---

## The Core Insight

When you give an AI agent a single, well-defined role, several things happen:

1. **The system prompt gets tighter** — fewer edge cases to handle
2. **The model behaves more consistently** — clear persona = predictable output
3. **Routing becomes obvious** — you know which agent handles what
4. **Failure modes are bounded** — an agent can't hallucinate outside its domain if it's defined to defer

Compare:
- ❌ "You are a helpful assistant. Help me with anything."
- ✅ "You are Matlock — a careful legal analyst. You review contracts, flag risk clauses, and research legal questions. You always recommend consulting a real lawyer for consequential decisions."

The second agent is infinitely more useful in its domain — and safer, because it knows its limits.

---

## The Four Parts of a Good Agent

### 1. Persona
Who are they? What's their personality? Give them a name that signals their energy.

```yaml
# Names carry priors. Choose carefully.
# "Matlock" → careful, detail-oriented, experienced
# "Vega" → quantitative, analytical, precise  
# "Riley" → creative, communicative, adaptable
```

### 2. Domain
What do they own? Be specific. Overlap creates confusion.

```yaml
# Good domain definition:
domain: |
  - Contract review and risk analysis
  - Legal research and precedent lookup
  - Compliance questions
  - Terms of service analysis

# Not good:
domain: "legal stuff"
```

### 3. Boundaries
What do they explicitly NOT do? This is as important as what they do.

```yaml
# Clear boundaries prevent agent sprawl:
not_my_job: |
  - Financial advice (that's Vega)
  - Writing client emails (that's Riley)
  - Making final decisions (human only)
  - Anything requiring a licensed attorney for real-world consequences
```

### 4. Escalation
When do they defer to humans or other agents?

```yaml
escalation: |
  - "If the legal risk is significant, always flag for human review"
  - "If this requires jurisdiction-specific advice, recommend a real lawyer"
  - "If you're uncertain, say so explicitly"
```

---

## Model Selection

Match the model to the task complexity and volume:

| Task Type | Model | Why |
|-----------|-------|-----|
| Legal review, long-form writing, complex reasoning | Opus | Precision matters; cost is acceptable |
| Research, analysis, coding, most work | Sonnet | Great quality, reasonable speed |
| Email triage, routing, simple queries, high-volume | Haiku | Cheap at scale; sufficient for classification |

**Rule of thumb:** Use Haiku for anything that runs 100x/day. Use Opus only for work you'd reread carefully. Sonnet for everything else.

---

## The Persona Trick

Name your agents after people (real or fictional) who embody the right energy. LLMs have strong priors on famous personas.

- **Obama** (as Barack) → strategic, measured, good at synthesis
- **Olivia Benson** (as Olivia) → relentless, no excuses, detail-focused
- **Matlock** → cautious, thorough, experienced, trusted
- **Vega** → mathematical, analytical, precise

You don't need to explicitly describe these qualities at length — the name does some of the work. But do layer your own domain-specific instructions on top.

---

## Voice Assignment Philosophy

Give agents voices that match their persona and your relationship to them:

- **Authority figures** get authoritative voices (Obama, Morgan Freeman)
- **Enforcement agents** get direct voices (Olivia Benson)  
- **Approachable agents** get warm voices (Taylor Swift for teaching/personal)
- **Gravitas agents** get gravitas voices (David Attenborough for legal)
- **High-volume/text agents** skip voice entirely (email triage doesn't need audio)

---

## Templates

Each agent file in this directory follows this structure:

```yaml
# [AGENT NAME] — [ROLE]
# Model: [model choice + reasoning]
# Voice: [voice + reasoning]

id: [lowercase-id]
name: [Display Name]
model: [model]
voice: [voice-id or "text-only"]

system: |
  [Full system prompt]
  
  PERSONA: [Who they are]
  DOMAIN: [What they own]
  BOUNDARIES: [What they don't do]
  ESCALATION: [When to defer]
  STYLE: [Communication style]
```

---

## Starting Point

If you're building your own agent team, start with 3:

1. **A Chief of Staff** — routes everything, handles general queries
2. **A Researcher** — deep analysis and information synthesis
3. **A Writer** — drafts and edits communications

Add agents when you notice the Chief handling a domain consistently. That's a signal you need a specialist.
