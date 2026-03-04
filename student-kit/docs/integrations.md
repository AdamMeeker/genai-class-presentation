# Integration Guide

> How to connect your agent system to the tools you already use

---

## Integration Philosophy

**Pull vs. Push:**
- **Pull:** Your agent fetches data when asked ("What's on my calendar?")
- **Push:** External events trigger agent actions (new email → Inbox agent runs)

Start with Pull. It's simpler and immediately useful. Add Push integrations as you learn the patterns.

**Local vs. Cloud APIs:**
- Prefer local APIs when possible (Home Assistant, local databases)
- Cloud APIs are fine but add latency and API key management
- Always use environment variables for credentials — never hardcode

---

## Microsoft 365 / Graph API

The Microsoft Graph API is the hidden superpower of M365. It exposes everything: calendar, email, tasks, files, Teams, SharePoint.

### Setup

1. **Register an app in Azure AD:**
   - Go to portal.azure.com → Azure Active Directory → App registrations
   - New registration → give it a name
   - Supported account types: "Accounts in this organizational directory only"
   - Redirect URI: http://localhost:3000/auth/callback (for OAuth flow)

2. **Grant permissions:**
   - API permissions → Add permission → Microsoft Graph
   - Delegated: `Calendars.ReadWrite`, `Tasks.ReadWrite`, `Mail.Read`, `User.Read`
   - Grant admin consent (if your account allows it)

3. **Get credentials:**
   - Overview page: Application (client) ID
   - Certificates & secrets → New client secret → copy the value

4. **OAuth flow (use the prompts/build-integrations.md Claude Code prompt)**

### Key Endpoints

```bash
# Calendar: today's events
GET /v1.0/me/calendarview?startDateTime=2026-03-03T00:00&endDateTime=2026-03-03T23:59

# Planner: my tasks
GET /v1.0/me/planner/tasks

# Create Planner task
POST /v1.0/planner/tasks
Body: { "planId": "xxx", "title": "Task name", "dueDateTime": "2026-03-07T00:00:00Z" }

# Email: unread inbox
GET /v1.0/me/mailFolders/inbox/messages?$filter=isRead eq false
```

### Agent Configuration Example

```yaml
# In Barack's system prompt:
TOOLS:
- calendar: Get today's events from Microsoft Calendar
- planner_list: List open tasks from Microsoft Planner  
- planner_create: Create a new task in Microsoft Planner
```

---

## Apple Calendar / iCal

Simpler than M365 if you're on macOS with iCloud Calendar:

```bash
# macOS has a CalDAV-accessible local calendar store
# Or use the icalbuddy CLI tool:

brew install ical-buddy

# Today's events:
icalbuddy eventsToday

# This week:
icalbuddy eventsFrom:today to:today+7
```

Wrap this in a simple REST API that your agents call:

```typescript
// GET /calendar/today
const result = execSync('icalbuddy eventsToday').toString();
return parseIcalBuddyOutput(result);
```

---

## Home Assistant

### Setup

1. Install HAOS on a VM (see hardware.md)
2. Enable REST API: Settings → Profile → Long-lived access tokens → Create
3. Test: `curl http://[HA_IP]:8123/api/ -H "Authorization: Bearer [TOKEN]"`

### Key API Calls

```bash
# Get all states
GET /api/states

# Control a light
POST /api/services/light/turn_on
Body: { "entity_id": "light.living_room" }

# Set thermostat
POST /api/services/climate/set_temperature
Body: { "entity_id": "climate.main", "temperature": 68 }

# Lock all doors
POST /api/services/lock/lock
Body: { "entity_id": ["lock.front_door", "lock.back_door"] }
```

### Agent Use Cases

```
"Turn off the kitchen lights" → Quinn calls HA service
"Is the garage door open?" → Quinn reads garage door sensor state
"Lock everything, I'm leaving" → Quinn calls lock_all shortcut
Morning briefing → Barack reads temperature + locked status
```

### Automations via n8n

```
n8n workflow: 
When [HA event: door unlocked at night] 
→ Notify Barack
→ Barack sends Signal alert
→ "Front door unlocked at 11:47 PM — was this you?"
```

---

## Signal

Signal is the primary messaging channel — it's end-to-end encrypted, so sensitive agent conversations stay private.

### OpenClaw Signal Setup

```bash
# Link Signal to OpenClaw
openclaw channel link signal
# Scan QR code with Signal on phone

# Or configure manually in config.json:
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

### Sending from Agents to Signal

Agents send messages to Signal automatically via OpenClaw. No additional config needed once the channel is linked.

For cron-based notifications:
```yaml
crons:
  - id: morning-briefing
    schedule: "0 7 * * 1-5"
    agent: chief
    message: "Generate morning briefing"
    channel: signal  # Sends response to Signal
```

---

## Email (Apple Mail)

### Reading Email

Apple Mail stores messages locally in SQLite. You can read it directly:

```bash
# Location (macOS):
~/Library/Mail/V10/MailData/Envelope\ Index

# Requires Full Disk Access in Privacy settings
# System Settings → Privacy & Security → Full Disk Access → add your terminal
```

### Building a Mail Reader (use prompts/build-integrations.md)

The Claude Code prompt in `prompts/build-integrations.md` will build a REST API that reads Apple Mail for you. Your Inbox agent calls this API.

### Gmail / IMAP Alternative

```json
{
  "email": {
    "provider": "imap",
    "host": "imap.gmail.com",
    "port": 993,
    "user": "you@gmail.com",
    "password": "your-app-password"
  }
}
```

Note: For Gmail, you need an App Password (not your regular password).
Settings → Security → 2FA enabled → App passwords

---

## Obsidian

Obsidian is a markdown-based note-taking app that syncs via iCloud. Your agents can read and write to it:

```bash
# Obsidian vault is just a directory of .md files
# Default location:
~/Library/Mobile Documents/iCloud~md~obsidian/Documents/[vault-name]/

# Your agent can search:
grep -r "search term" ~/Library/Mobile\ Documents/...

# Or write:
echo "## Note content" >> ~/Library/Mobile\ Documents/.../daily/2026-03-03.md
```

### Agent Use Cases

```
Marcus: Search Obsidian for past research on [topic]
Riley: Write draft to Obsidian for review
Barack: Create daily note with briefing content
```

---

## n8n

n8n is a self-hosted workflow automation tool (like Zapier, but local and free).

### Key Use Cases

**Email → Task:**
```
Trigger: New email with [TASK] in subject
Action: Parse task from email
Action: POST to OpenClaw/Barack to create Planner task
Action: Mark email read
```

**Morning Briefing:**
```
Trigger: Cron 7:00 AM weekdays
Action: GET today's calendar from M365
Action: GET open tasks from Planner
Action: GET weather from wttr.in
Action: Build briefing text
Action: POST to OpenClaw/Barack
Action: Barack responds via Signal with TTS
```

**Invoice → Notification:**
```
Trigger: Invoice Ninja webhook (payment received)
Action: POST to OpenClaw/Sterling
Action: Sterling notifies via Signal + updates records
```

### Connecting n8n to OpenClaw

```
HTTP Request node in n8n:
URL: http://localhost:18789/api/agents/barack/message
Method: POST
Headers: Content-Type: application/json
Body: { "message": "{{your_built_message}}" }
```

---

## Invoice Ninja

Self-hosted invoicing. Runs in Docker.

### Docker Compose

```yaml
services:
  invoiceninja:
    image: invoiceninja/invoiceninja:latest
    ports:
      - "8082:80"
    environment:
      APP_URL: https://clients.yourdomain.com
      DB_HOST: db
      DB_DATABASE: ninja
    volumes:
      - ./storage:/var/www/app/storage
  db:
    image: mariadb:latest
    environment:
      MYSQL_DATABASE: ninja
      MYSQL_ROOT_PASSWORD: changeme
```

### API Access

```bash
# Get all invoices
GET /api/v1/invoices
Header: X-Api-Token: your-api-token

# Create invoice
POST /api/v1/invoices
```

### Agent Integration

Sterling can create invoices:
```
User: "Create an invoice for Client X, $5000, net 30"
Sterling: Creates invoice via API → Sends confirmation → Logs to memory
```

---

## DocuSeal

Self-hosted contract signing. Docker-based.

### Setup

```yaml
services:
  docuseal:
    image: docuseal/docuseal:latest
    ports:
      - "8083:3000"
    volumes:
      - ./storage:/app/storage
```

### Workflow

1. Matlock reviews contract draft
2. Sterling creates DocuSeal document from template
3. DocuSeal sends signature request to client
4. Webhook fires when signed → n8n → OpenClaw

---

## Quick Reference: Port Map

```
OpenClaw Gateway:     localhost:18789
Mission Control:      localhost:3000
Chatterbox TTS:       localhost:4126
n8n:                  10.0.0.120:5678  (or localhost:5678)
Home Assistant:       10.0.0.124:8123
Invoice Ninja:        localhost:8082
DocuSeal:             localhost:8083
Proxmox Node 1:       10.0.0.55:8006
Proxmox Node 2:       10.0.0.70:8006
```
