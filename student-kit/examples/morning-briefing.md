# Example: Morning Briefing Automation

> A complete walkthrough of a real automation — from cron trigger to
> Obama-voiced briefing playing in the kitchen at 6:45 AM.

---

## What It Does

Every weekday morning at 6:45 AM, the system automatically:

1. Barack wakes up (cron fires)
2. Reads today's calendar from Microsoft 365
3. Reads open tasks from Microsoft Planner
4. Gets the weather for Tiffin, Iowa
5. Checks for any urgent email flags from Inbox agent
6. Synthesizes a 60-90 second briefing
7. Sends it to Signal with TTS
8. Plays it via Sonos in the kitchen (optional)

**No manual action required. Not even opening a phone.**

---

## The Components

```
CRON TIMER (6:45 AM weekdays)
        │
        ▼
  n8n WORKFLOW
  ├── GET Calendar (Graph API)
  ├── GET Tasks (Planner API)  
  ├── GET Weather (wttr.in)
  └── GET Email flags (from Inbox agent)
        │
        ▼
  BARACK (Claude Sonnet 4.6)
  └── Synthesize briefing text
        │
        ▼
  CHATTERBOX TTS (Obama voice, MPS)
  └── Generate audio file
        │
        ├── Signal: Send audio message
        └── Sonos: Play in kitchen (HTTP API)
```

---

## Step 1: The n8n Workflow

Build this in n8n at http://[N8N_IP]:5678:

### Node 1: Schedule Trigger
```
Type: Schedule Trigger
Schedule: Cron expression
Value: 45 6 * * 1-5   (6:45 AM, Monday-Friday)
```

### Node 2: Get Calendar (HTTP Request)
```
URL: https://graph.microsoft.com/v1.0/me/calendarview
Method: GET
Headers:
  Authorization: Bearer {{$credentials.m365.accessToken}}
  Prefer: outlook.timezone="America/Chicago"
Query params:
  startDateTime: {{$today.format('YYYY-MM-DDT00:00:00')}}
  endDateTime: {{$today.format('YYYY-MM-DDT23:59:59')}}
  $select: subject,start,end,location,organizer
  $orderby: start/dateTime
```

### Node 3: Get Tasks (HTTP Request)
```
URL: https://graph.microsoft.com/v1.0/me/planner/tasks
Method: GET
Headers:
  Authorization: Bearer {{$credentials.m365.accessToken}}
Query params:
  $filter: percentComplete ne 100
  $select: title,dueDateTime,priority
  $orderby: dueDateTime
  $top: 10
```

### Node 4: Get Weather (HTTP Request)
```
URL: https://wttr.in/Tiffin+Iowa
Method: GET
Query params:
  format: 3   (returns: "Tiffin, Iowa: ⛅ +45°F")
```

### Node 5: Build Briefing Message (Code Node)
```javascript
// Combine all data into a briefing prompt
const calendar = $('Get Calendar').first().json.value;
const tasks = $('Get Tasks').first().json.value;
const weather = $('Get Weather').first().json.body;

// Format calendar
const calText = calendar.length === 0 
  ? "No meetings today."
  : calendar.map(e => {
      const time = new Date(e.start.dateTime).toLocaleTimeString('en-US', 
        { hour: 'numeric', minute: '2-digit', hour12: true });
      return `${time}: ${e.subject}`;
    }).join('\n');

// Format tasks (top 3 most urgent)
const urgentTasks = tasks
  .filter(t => t.dueDateTime)
  .sort((a,b) => new Date(a.dueDateTime) - new Date(b.dueDateTime))
  .slice(0, 3)
  .map(t => `- ${t.title} (due ${new Date(t.dueDateTime).toLocaleDateString()})`)
  .join('\n');

const prompt = `Good morning. Please give me my morning briefing.

Today is ${new Date().toLocaleDateString('en-US', { weekday: 'long', month: 'long', day: 'numeric' })}.

WEATHER: ${weather}

TODAY'S CALENDAR:
${calText}

PRIORITY TASKS (due soonest):
${urgentTasks || "No tasks with due dates."}

Please synthesize this into a 60-90 second spoken briefing.
Keep it energetic but brief. Lead with weather + calendar, then tasks.
End with one motivating thought for the day.
Format for voice: no bullet points, no markdown, just clean prose.`;

return [{ json: { prompt } }];
```

### Node 6: Send to Barack (HTTP Request)
```
URL: http://192.168.1.100:18789/api/agents/barack/message
Method: POST
Body (JSON):
{
  "message": "{{$json.prompt}}",
  "tts": true,
  "voice": "obama",
  "responseFormat": "audio"
}
```

### Node 7: Send to Signal (HTTP Request or use OpenClaw's built-in)
```
OpenClaw handles Signal delivery when tts:true is set.
The audio file is automatically sent to the configured Signal number.
```

### Node 8 (Optional): Play on Sonos (HTTP Request)
```
URL: http://[SONOS_SPEAKER_IP]:1400/MediaRenderer/AVTransport/Control
(See Sonos HTTP API documentation for the SOAP request format)

Alternatively: use the openclaw Sonos integration if configured.
```

---

## Step 2: Configure the Cron in OpenClaw

If you prefer OpenClaw-native cron instead of n8n:

```yaml
# ~/myai/config/crons.yaml

crons:
  - id: morning-briefing
    schedule: "45 6 * * 1-5"
    agent: barack
    channel: signal
    tts: true
    voice: obama
    message: |
      Good morning. Generate my morning briefing.
      
      Today is {date}.
      
      Please check:
      1. Today's calendar (use calendar tool)
      2. My top 3 priority tasks (use planner tool)
      3. Current weather in Tiffin, Iowa (use weather tool)
      
      Synthesize into a 60-90 second spoken briefing.
      Energetic, brief, no markdown formatting.
      End with one sentence of motivation.
```

Barack needs the calendar, planner, and weather tools configured as OpenClaw tools.

---

## Step 3: Barack's Briefing System Prompt Addition

Add this to Barack's system prompt:

```
MORNING BRIEFING FORMAT:
When asked to generate a morning briefing, format your response as
clean spoken prose (no bullet points, no headers, no markdown).
Structure:
1. Greeting + date/day
2. Weather (one sentence)
3. Calendar highlights (top 2-3 events)
4. Top priorities (top 2-3 tasks)
5. One motivating close

Target: 60-90 seconds when read aloud at normal pace.
Test: ~150-225 words.
```

---

## Sample Briefing Output

Barack generates something like:

> "Good morning. It's Tuesday, March 3rd. 
> 
> Weather in Tiffin: partly cloudy and 34 degrees, warming to the low 40s this afternoon — grab a jacket.
> 
> Today's calendar: you've got a 10 AM standup with the Agilent CX team — 30 minutes, just status updates. Then your MSBA lecture at 2 PM, which runs two hours. You're clear in the morning, so that's your focus block.
> 
> On the priority list: the Q1 analytics deck is due Thursday and needs your final review. The consulting proposal for that Iowa City client is still in draft — Riley can finish it if you give her the green light. And there are two student emails asking about the midterm rubric that Sage already drafted responses for.
> 
> You have a focused morning in front of you. Use it. Let's go."

---

## What It Actually Costs

**Per briefing:**
- Barack (Claude Sonnet 4.6): ~$0.008 (roughly 1,200 input tokens + 250 output)
- Chatterbox TTS: $0 (local)
- n8n: $0 (self-hosted)

**Per month (22 weekdays):**
- ~$0.18 in Claude API costs
- Everything else: zero

**vs. a dedicated morning briefing app:** $10-20/month for far less customization.

---

## Extending the Briefing

Once the basic briefing works, add:

**Stock/portfolio summary** (via Vega):
- Pre-market snapshot of your watchlist

**Inbox summary** (via Inbox agent):
- Urgent emails from overnight

**Kids' schedule** (via Quinn):
- If Kid has soccer, leave by 5:15 PM

**Weekly version** (Monday only):
- Full week preview + last week's wins

The pattern: each extension is a new data source → new tool → more context for Barack → richer briefing.
