# Prompt: Build Your Integrations

> Copy-paste these prompts into Claude Code to wire up integrations
> for your AI agent system. Use the section for the integration you want.

---

## Integration 1: Microsoft 365 / Planner

```
Help me integrate Microsoft Planner with my OpenClaw agent system so my
Chief of Staff agent can create, read, and update tasks.

## Setup
- I have a Microsoft 365 account: [YOUR_EMAIL]
- OpenClaw is running at localhost:18789
- My workspace is at ~/myai/

## What I Need
1. Microsoft Graph API authentication setup (OAuth 2.0)
   - Register an app in Azure AD (walk me through the steps)
   - Get client_id, client_secret, tenant_id
   - Token auto-refresh so I don't need to manually re-auth

2. A planner.ts TypeScript module that provides:
   - listTasks(planId: string): Get tasks from a plan
   - createTask(planId: string, title: string, dueDate?: string): Create a task
   - completeTask(taskId: string): Mark a task complete
   - updateTask(taskId: string, updates: Partial<Task>): Update a task

3. OpenClaw tool configuration so my agent can call these functions

4. A test script: ts-node test-planner.ts

## Graph API Endpoints
- List tasks: GET /v1.0/planner/plans/{planId}/tasks
- Create task: POST /v1.0/planner/tasks
- Update task: PATCH /v1.0/planner/tasks/{taskId}
- Get my plans: GET /v1.0/me/planner/plans

## My Plan IDs
[PASTE YOUR PLANNER PLAN IDs here — you can find them in the Planner URL]

## File Structure
~/myai/integrations/
├── m365/
│   ├── graph-client.ts    # Authenticated MS Graph client
│   ├── planner.ts         # Planner operations
│   ├── token-store.json   # Token storage (add to .gitignore)
│   └── test-planner.ts    # Test script

## Tech
- TypeScript + bun
- @microsoft/microsoft-graph-client or direct fetch
- Store tokens in ~/myai/integrations/m365/token-store.json
```

---

## Integration 2: Email (Apple Mail via SQLite)

```
Help me build an email reader for Apple Mail on macOS so my Inbox agent
can triage email.

## How Apple Mail Works
Apple Mail stores messages in a SQLite database at:
~/Library/Mail/V10/MailData/Envelope Index

This database has tables: messages, subjects, addresses, etc.

## What I Need
Build a TypeScript/Node.js email reader that:

1. Reads unread messages from Apple Mail's SQLite database
2. Returns structured email objects:
   {
     id: string,
     from: string,
     subject: string,
     date: Date,
     preview: string,  // first 500 chars of body
     mailbox: string,
     isRead: boolean
   }
3. Can mark messages as read
4. Filters by: unread only, since date, from address

## API
Build a simple REST API (Express or Hono) on port 4127:
- GET /emails?unread=true&since=2024-01-01
- POST /emails/:id/read

## Considerations
- Full Disk Access required in macOS Privacy settings
- Handle WAL mode SQLite correctly
- Messages may be in .emlx files separately
- Body content may need to be read from the filesystem

## After Building
I'll wire this to my OpenClaw Inbox agent as a tool the agent
can call to get email data.

## File Structure
~/myai/integrations/email/
├── server.ts          # REST API
├── apple-mail.ts      # Mail reading logic
└── test-email.ts      # Test script
```

---

## Integration 3: Home Assistant

```
Help me integrate Home Assistant with my OpenClaw agent system so I can
control my smart home through my AI agents.

## Setup
- Home Assistant URL: http://[HA_IP]:8123
- Long-lived access token: [GET FROM HA → Profile → Long-lived tokens]
- OpenClaw at localhost:18789

## What I Need
1. A homeassistant.ts module with:
   - getStates(): Get all entity states
   - getState(entityId: string): Get a specific entity state
   - callService(domain: string, service: string, entityId: string, data?: object)
   - turnOn(entityId: string)
   - turnOff(entityId: string)
   - setTemperature(entityId: string, temp: number)
   - getLights(): Get all light entities
   - getLocks(): Get all lock entities
   - getSensors(): Get all sensor states

2. OpenClaw tool definitions so my agents can call these

3. Common commands as named shortcuts:
   - lockAll(): Lock all door locks
   - morningRoutine(): Lights to 100%, thermostat to 68
   - bedtimeRoutine(): Lights off, locks on, thermostat to 65

## HA REST API
Base URL: http://[HA_IP]:8123/api
Auth: Authorization: Bearer [TOKEN]
- GET /api/states — all states
- GET /api/states/{entity_id} — specific entity
- POST /api/services/{domain}/{service} — call a service

## My Entities
[PASTE YOUR KEY ENTITY IDs HERE]
Example:
- light.living_room
- lock.front_door
- climate.thermostat
- sensor.front_door_battery

## File Structure
~/myai/integrations/homeassistant/
├── client.ts          # HA REST client
├── shortcuts.ts       # Named automation shortcuts
├── types.ts           # Entity types
└── test-ha.ts         # Test script
```

---

## Integration 4: Google/Outlook Calendar

```
Help me build a calendar reader so my AI agents can see my schedule.

## Calendar Source (choose one)
[ ] Google Calendar — I'll use the Google Calendar API
[ ] Outlook/M365 Calendar — I'll use Microsoft Graph API

## What I Need
For [CHOSEN CALENDAR]:

1. Authentication setup and token management
2. A calendar.ts module with:
   - getToday(): Events happening today
   - getUpcoming(days: number): Events in next N days
   - getEvent(eventId: string): Single event details
   - Event type: { id, title, start, end, location, description, attendees }

3. OpenClaw tool so agents can query the calendar

## Agent Usage Examples
- Barack morning briefing: "What's on my calendar today?"
- Taylor scheduling: "Am I free Thursday at 2 PM?"
- General: "When's my next meeting?"

## Auth Approach
For [Google]: OAuth 2.0, store token in ~/.myai/integrations/calendar/token.json
For [M365]: Same as Planner integration (reuse the Graph client)

## File Structure
~/myai/integrations/calendar/
├── auth.ts           # OAuth flow
├── calendar.ts       # Calendar operations
└── test-calendar.ts  # Test
```

---

## Integration 5: n8n Workflow Automation

```
Help me set up n8n as a workflow automation layer that connects to my
OpenClaw agent system.

## Setup
n8n is running at http://[N8N_IP]:5678 (or localhost:5678)

## What I Need

1. OpenClaw webhook receiver in n8n:
   - Webhook URL that receives events from OpenClaw
   - Routes events to different workflows based on type

2. Three starter workflows:
   
   a) Morning Briefing Trigger (runs 7:00 AM weekdays)
      → GET calendar events (from Google/Outlook)
      → GET open tasks (from Planner/Todoist)
      → GET weather (from wttr.in API)
      → POST to OpenClaw/Barack with all data
      → Barack synthesizes and responds via Signal
   
   b) Email → Task (runs every 30 minutes)
      → GET unread emails with [TASK] in subject
      → Parse task from email body
      → POST to OpenClaw/Barack to create task
      → Mark email as read
   
   c) Daily Digest (runs 6:00 PM weekdays)
      → GET today's completed tasks
      → GET tomorrow's calendar
      → POST to OpenClaw/Barack for evening summary
      → Send to Signal

3. n8n credential setup for:
   - OpenClaw REST API (localhost:18789)
   - [YOUR EMAIL PROVIDER]
   - [YOUR CALENDAR PROVIDER]

## n8n HTTP Node Config for OpenClaw
URL: http://localhost:18789/api/agents/barack/message
Method: POST
Body: { "message": "{{your_message_variable}}" }

## Export workflows as JSON so I can import them into n8n

Walk me through setting up the n8n credentials first, then
build the workflows one at a time. Start with the Morning Briefing.
```

---

## Tips for All Integrations

### Testing Before Wiring to Agent
Always test integrations standalone first:
```bash
bun run test-planner.ts  # Does it return data?
bun run test-ha.ts       # Can it control a light?
```

Then wire to OpenClaw. Debugging is much easier in isolation.

### Environment Variables
Never hardcode credentials. Use a `.env` file:
```
M365_CLIENT_ID=xxx
M365_CLIENT_SECRET=xxx
HA_TOKEN=xxx
```

Add `.env` and `token-store.json` to `.gitignore` before anything else.

### Rate Limiting
Most APIs have rate limits. Add delays to polling loops:
- Email polling: every 30 minutes is plenty
- Calendar: cache for 5 minutes minimum
- HA states: real-time is fine via WebSocket, polling every 60s for REST
