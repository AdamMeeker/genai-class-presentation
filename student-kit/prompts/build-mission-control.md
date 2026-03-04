# Prompt: Build Mission Control Dashboard

> Copy-paste this into Claude Code to build your personal AI dashboard.  
> Customize the sections marked with [BRACKETS] before pasting.

---

## The Prompt

```
You are building "Mission Control" — a personal AI command center dashboard
for my OpenClaw-powered agent system.

## Tech Stack
- Next.js 15 (App Router)
- TypeScript (strict mode)
- Tailwind CSS
- shadcn/ui components
- Recharts for data visualization
- bun as package manager (NOT npm)
- Run on localhost:3000

## Design System
- Background: #0a0a0f (near black)
- Card background: #0f0f1a
- Primary accent: #00d9ff (cyan)
- Secondary accent: #a855f7 (purple)
- Success: #22c55e (green)
- Warning: #fbbf24 (yellow)
- Text: #e2e8f0
- Muted text: #94a3b8
- Border: rgba(0,217,255,0.2)
- Font: system-ui / Segoe UI
- All cards have subtle glow on hover
- Dark, professional, not corporate

## OpenClaw API (running at http://localhost:18789)
The dashboard connects to OpenClaw's REST API. Key endpoints:
- GET /api/agents — list all agents with status
- POST /api/agents/{id}/message — send a message to an agent
- GET /api/sessions — list recent sessions
- GET /api/crons — list scheduled jobs
- GET /api/memory/today — today's memory file content
- GET /api/memory/longterm — MEMORY.md content
- GET /api/channels — connected channels status

If you're not sure about an endpoint, check the OpenClaw docs at
https://openclaw.ai/docs/api or make a reasonable REST API call
and I'll test and correct.

## Pages to Build

### 1. Dashboard (/)
- Summary cards: agents online, tasks due today, unread emails,
  upcoming calendar events
- Quick message input (send to default agent)
- Recent activity feed (last 10 agent interactions)
- System health indicators (gateway status, API status)

### 2. Agents (/agents)
- Grid of all configured agents
- Each card: emoji, name, role, model, status (active/idle),
  last interaction time
- Click agent → open chat panel with that agent
- Voice button per agent (if TTS configured)

### 3. Chat (/chat or per-agent)
- Full chat interface per agent
- Message history
- Voice input button (Web Speech API)
- Voice output toggle
- Model indicator
- Context injection indicator (memory loaded)

### 4. Tasks (/tasks)
- Task list with status, due dates, priority
- Quick add task
- Connects to: [YOUR_TASK_SOURCE — e.g., Microsoft Planner API,
  local JSON file, or leave placeholder]
- Filter by: due today, overdue, by project

### 5. Memory (/memory)
- Today's memory file (readable/editable)
- Long-term MEMORY.md viewer
- Search across memory files
- Timeline view of daily files

### 6. System (/system)
- OpenClaw gateway status
- All configured agents and their models
- Cron job list with next run times
- Channel connection status (Signal, iMessage, Email)
- Recent error log

## My Agents
[PASTE YOUR AGENT LIST HERE — names, roles, models, emoji]
Example:
- 🎩 Barack — Chief of Staff — claude-sonnet-4-6
- 🔍 Marcus — Research — claude-sonnet-4-5
- ✍️ Riley — Writing — claude-opus-4-5

## File Structure
```
mission-control/
├── app/
│   ├── layout.tsx          # Root layout with sidebar
│   ├── page.tsx            # Dashboard
│   ├── agents/page.tsx
│   ├── chat/[agentId]/page.tsx
│   ├── tasks/page.tsx
│   ├── memory/page.tsx
│   └── system/page.tsx
├── components/
│   ├── sidebar.tsx         # Navigation sidebar
│   ├── agent-card.tsx      # Reusable agent card
│   ├── chat-panel.tsx      # Chat interface
│   ├── stat-card.tsx       # Dashboard stat cards
│   └── voice-input.tsx     # Web Speech API component
├── lib/
│   ├── openclaw.ts         # OpenClaw API client
│   └── types.ts            # TypeScript types
└── public/
```

## Implementation Notes
- Use client components for anything with real-time updates
- Server components for initial data loading
- No authentication needed (local use only)
- Error states should be informative, not just "Error"
- Loading states with skeleton screens
- Responsive but primarily designed for desktop/1280px

## Start with
1. The layout with sidebar navigation
2. Dashboard page with mock data (we'll wire up the real API after)
3. Agents page with agent cards

Then we'll iterate from there.
```

---

## After Pasting

Claude Code will ask clarifying questions and then build. The typical flow:

1. **Iteration 1:** Basic structure + Dashboard + Agents page with mock data
2. **Iteration 2:** Wire up OpenClaw API, add Chat page
3. **Iteration 3:** Tasks, Memory, System pages + polish

For each iteration, run the app and test, then tell Claude Code:
- What works
- What needs fixing
- What to build next

---

## Customization Points

Before pasting, update these:
- **Agent list:** Replace with your actual agents
- **Task source:** Replace `[YOUR_TASK_SOURCE]` with your actual task system
- **Additional pages:** Add any domain-specific pages you want
  (e.g., a Trading page if you have Vega, a Teaching page if you teach)

---

## Time Estimate

- Setup + first iteration: 30-45 minutes
- Full 6-page dashboard: 3-4 hours of iteration
- Polish + real API integration: another 2-3 hours

Total: ~8 hours of human time. (Claude Code is doing most of the work.)
