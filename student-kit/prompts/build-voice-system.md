# Prompt: Build Your Voice System

> Copy-paste this into Claude Code to set up Chatterbox TTS with voice
> cloning and integrate it with OpenClaw.

---

## Prerequisites

- Mac with Apple Silicon (M1/M2/M3/M4) for MPS acceleration
  (works on Intel too, just slower)
- Python 3.11+ and uv installed
- OpenClaw running at localhost:18789
- A short audio sample (15-60 seconds) of the voice you want to clone
  - Must be clean audio, minimal background noise
  - WAV or MP3 format

---

## Part 1: Install Chatterbox TTS

```bash
# Install uv if you don't have it
curl -LsSf https://astral.sh/uv/install.sh | sh

# Create a directory for TTS
mkdir ~/tts && cd ~/tts

# Create a virtual environment
uv venv
source .venv/bin/activate

# Install Chatterbox
uv pip install chatterbox-tts

# Test it works (no voice clone, just default voice)
python -c "
from chatterbox.tts import ChatterboxTTS
import torchaudio
model = ChatterboxTTS.from_pretrained(device='mps')  # 'cpu' if no Apple Silicon
wav = model.generate('Hello, this is a test.')
torchaudio.save('test.wav', wav, model.sr)
print('Success! Check test.wav')
"
```

---

## Part 2: Clone a Voice

```bash
# Clone a voice from your audio sample
python -c "
from chatterbox.tts import ChatterboxTTS
import torchaudio

model = ChatterboxTTS.from_pretrained(device='mps')

# Path to your audio sample (15-60 seconds)
VOICE_SAMPLE = '/path/to/your/voice-sample.wav'
VOICE_NAME = 'myvoice'

wav = model.generate(
    'This is a test of my cloned voice. Testing one two three.',
    audio_prompt_path=VOICE_SAMPLE,
    exaggeration=0.5,  # 0.0 = subtle clone, 1.0 = strong exaggeration
)
torchaudio.save(f'{VOICE_NAME}-test.wav', wav, model.sr)
print(f'Voice clone test saved to {VOICE_NAME}-test.wav')
"

# Listen to it — adjust exaggeration (0.3-0.7 is usually good range)
open myvoice-test.wav
```

---

## Part 3: Build the TTS Server

Use this prompt with Claude Code:

```
I need a Chatterbox TTS server that acts as an ElevenLabs-compatible API,
so OpenClaw can use it for text-to-speech.

## Requirements

Build a Python FastAPI server that:

1. Accepts requests in ElevenLabs format:
   POST /v1/text-to-speech/{voice_id}
   Body: {"text": "...", "model_id": "..."}
   Returns: audio/mpeg (MP3 file)

2. Supports multiple voice clones (one per voice_id)

3. Uses Chatterbox TTS with MPS acceleration (Apple Silicon)

4. Starts fast (model loads once on startup)

5. Handles concurrent requests (async)

## Voice Configuration
Create a voices.json config file:
{
  "voices": {
    "myvoice": {
      "sample_path": "/path/to/sample.wav",
      "exaggeration": 0.5,
      "display_name": "My Voice"
    },
    "default": {
      "sample_path": null,
      "exaggeration": 0.3,
      "display_name": "Default"
    }
  }
}

## Tech Stack
- Python with FastAPI
- uv for package management
- Chatterbox TTS (already installed in ~/tts/.venv)
- Run on port 4126
- Start with: python server.py

## File Structure
~/tts/
├── server.py          # FastAPI server
├── voices.json        # Voice configurations  
├── samples/           # Voice sample WAV files
│   └── myvoice.wav
├── cache/             # Generated audio cache (optional)
└── .venv/

## Extra Features
- Health check endpoint: GET /health
- List voices: GET /v1/voices
- Basic request caching by text+voice_id (optional, saves API calls)
- Logging with request duration

## After building
I'll test with:
curl -X POST http://localhost:4126/v1/text-to-speech/myvoice \
  -H "Content-Type: application/json" \
  -d '{"text": "Hello from my cloned voice"}' \
  --output test.mp3 && open test.mp3
```

---

## Part 4: Connect to OpenClaw

After the server is running, configure OpenClaw:

```json
// In ~/.openclaw/config.json, add:
{
  "tts": {
    "provider": "elevenlabs",
    "baseUrl": "http://localhost:4126",
    "apiKey": "local",
    "defaultVoice": "myvoice"
  }
}
```

Then per-agent voice configuration:
```yaml
# In your agent YAML:
id: chief
voice: myvoice   # matches the voice_id in your voices.json
```

Test it:
```bash
openclaw tts test "Hello, this is my AI assistant speaking."
```

---

## Part 5: Auto-Start on Login

```
Help me set up a macOS LaunchAgent to auto-start my Chatterbox TTS server
when I log in.

The server runs as: cd ~/tts && .venv/bin/python server.py
Port: 4126
Log file: ~/tts/chatterbox.log

Create:
1. ~/Library/LaunchAgents/com.myai.chatterbox.plist (LaunchAgent config)
2. ~/tts/start.sh (start script with proper env)

The LaunchAgent should:
- Start on login
- Restart if it crashes
- Log stdout and stderr to ~/tts/chatterbox.log
```

Load it:
```bash
launchctl load ~/Library/LaunchAgents/com.myai.chatterbox.plist
```

---

## Voice Quality Tips

- **Sample quality > sample length.** A clean 20s sample beats a noisy 2-minute one.
- **Exaggeration 0.3-0.5** works for most voices. Higher = more distinctive but potentially robotic.
- **Multiple samples** for the same voice (different sentences, same speaker) improves consistency.
- **Public domain voices:** Many historical speeches are in the public domain and make excellent samples.

## Ethical Considerations

- Only clone voices you have permission to use
- Never use cloned voices to impersonate real people in deceptive contexts
- Cloned celebrity voices for personal productivity (your own ears only) is generally acceptable
- For public presentations: use original samples or truly synthesized voices
