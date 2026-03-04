# Hardware Guide

> What to buy, what to skip, and why

---

## The Three Levels

### Level 1: Just Your Mac (~$0 new hardware)

Any Mac you already own works. The only requirement is that it can run OpenClaw and stay reasonably available.

| Mac | Suitability | Notes |
|-----|-------------|-------|
| MacBook Air (M1+) | ✅ Good for Level 1 | Great for development; closes = sleeps |
| MacBook Pro (M1+) | ✅ Good for Level 1-2 | Better battery for cron jobs |
| Mac mini (M1+) | ✅ Excellent for all levels | Always-on, silent, cheap to run |
| Mac mini M4 | 🏆 Recommended | Best value for always-on AI + TTS |
| iMac | ✅ Fine | Overkill; display unused |

**Recommendation for students:** Use your current Mac to start. Buy a dedicated machine when the value is proven.

---

### Level 2: Add a Dedicated Machine (~$600-800)

When you're ready for an always-on, dedicated compute node:

#### Mac mini M4 (~$600-800) — Adam's Primary
```
Mac mini M4 (2024)
- Apple M4 chip
- 16GB unified memory (get 16GB, not 8GB)
- 256GB SSD (fine for system + models)
- 10W idle power consumption
- Silent
- Fits in your hand
```

**Why Mac mini?**
- Apple Silicon (MPS) for local ML acceleration (Chatterbox TTS runs fast)
- macOS for iMessage, Apple Mail integration
- Always-on, silent, energy-efficient
- Value: $600-800 is 6-8 months of enterprise AI subscriptions

**What it runs:**
- OpenClaw (always on)
- Mission Control dashboard
- Chatterbox TTS (MPS-accelerated)
- All integrations and cron jobs

---

### Level 3: Home Server Nodes (~$150-300 each)

When you want to run services (Home Assistant, databases, media server, self-hosted tools) without burdening your primary compute:

#### Intel N100 Mini-PC — Adam's Proxmox Nodes (~$150-200)

```
Example units (check Amazon/AliExpress for current prices):
- Beelink EQ12 / EQ12 Pro
- Trigkey S5
- Minisforum UM350 / similar

Typical specs:
- Intel N100 / N95 / N200 processor
- 8-16GB RAM (upgrade to 16GB: ~$30)
- 256-512GB NVMe SSD
- 2.5Gb Ethernet
- ~15W TDP
- Silent or near-silent
```

**Why these over a full server?**
- $150-200 vs $800+ for a rack server
- 15W vs 200W+ power consumption
- Completely silent
- Runs Proxmox VE perfectly
- 16GB RAM runs 4-6 containers/VMs easily

**What to run on them:**
```
Node 1: Infrastructure services
├── Home Assistant (VM) — smart home
├── n8n (container) — workflow automation
├── AdGuard Home (container) — DNS/ad blocking
├── OPNsense (VM) — firewall/router
└── Metabase (container) — analytics

Node 2: Applications
├── Plex (or Jellyfin) — media server
├── Invoice Ninja (container) — invoicing
├── DocuSeal (container) — contracts
├── SearXNG (container) — private search
└── Gitea (container) — private Git
```

---

## Storage

### NAS (Network Attached Storage)

Not required for Level 1-2, but useful for Level 3:

**Budget option (~$300-400 total):**
- Synology DS223 2-bay NAS (~$200)
- 2x 4TB drives (~$80 each)
- Stores: media library, backups, large datasets

**DIY option:**
- Old PC + TrueNAS (free)
- Any drives you have

**For students:** Start without NAS. Add if you're storing significant media or need reliable backups.

---

## Networking

### What You Need

**Minimum (Level 1-2):**
- Your existing router is fine
- 1Gb Ethernet to your compute node if possible
- Wi-Fi is acceptable for development

**Better (Level 3):**
- Managed switch with VLAN support (~$50 TP-Link or similar)
- Separate VLANs for: IoT (Home Assistant), servers, personal devices
- OPNsense or pfSense as router/firewall (runs on N100)

**Not required:**
- 10Gb networking (overkill for home use)
- Enterprise switches
- Rack infrastructure

---

## Power Consumption & Cost

Running always-on hardware:

| Hardware | Idle Power | Monthly Cost (at $0.12/kWh) |
|----------|-----------|------------------------------|
| Mac mini M4 | ~6-10W | ~$0.75-1.25 |
| N100 Mini-PC | ~8-15W | ~$1-2 |
| Synology NAS (2-bay) | ~20-30W | ~$2-3 |
| **Full stack (2 nodes + mini)** | **~30-40W** | **~$3-5/month** |

Compare: A gaming PC at idle is 80-150W.

---

## Student Shopping List

### "I want to start this weekend" (no new hardware)
- ✅ Use your Mac laptop
- ✅ OpenClaw on your existing machine
- ✅ $0 hardware cost

### "I'm serious about building this" (one-time ~$600)
- Mac mini M4 (16GB) — $599-799
- This is your always-on AI gateway and TTS server

### "I want the full experience" (one-time ~$1,800-2,200)
- Mac mini M4 (16GB) — $700
- 2x N100 mini-PC — $150-200 each
- Upgrades (RAM, SSD) — $50-100 each
- Small managed switch — $40-60
- Cables, USB hub — $30

---

## What to Skip

**You don't need:**
- A NAS right away (start without it)
- 10Gb networking (unnecessary for this use case)
- A rack (overkill for a bedroom lab)
- High-end GPU (Chatterbox TTS runs fine on MPS/CPU)
- Multiple monitors for the server (you SSH in)
- A UPS initially (nice to have, not required)
- Enterprise hardware (N100 beats Xeon for this workload)

**Wait on:**
- More Proxmox nodes until you've filled node 1
- NAS until you have significant storage needs
- External GPU until local model inference matters more

---

## Proxmox: Getting Started

Proxmox VE is a free, open-source virtualization platform. Perfect for running multiple services on one mini-PC.

**Installation:**
1. Download Proxmox VE ISO from proxmox.com
2. Flash to USB with Balena Etcher
3. Boot your N100 from USB
4. Follow installer (10 minutes)
5. Access web UI at https://[IP]:8006

**Learning resources:**
- Proxmox VE documentation (excellent)
- Techno Tim on YouTube (great practical guides)
- r/homelab (community, very helpful)

**First containers to run:**
1. AdGuard Home (DNS blocking) — 5 minutes to set up
2. n8n (workflow automation) — 15 minutes
3. SearXNG (private search) — 10 minutes

**First VM to run:**
1. Home Assistant OS — 20 minutes, follow official guide

---

## Alternative: Cloud-First Approach

Don't want to run hardware? This works too:

**Cloud services to substitute:**
| Local | Cloud Alternative | Cost |
|-------|-------------------|------|
| Mac mini (OpenClaw) | OpenClaw on cloud VM | VPS ~$10-20/mo |
| Chatterbox TTS | ElevenLabs API | $5-22/mo |
| n8n local | n8n.cloud or Make.com | $20/mo |
| Home Assistant | Not possible (local-only) | — |
| Invoice Ninja | invoiceninja.com hosted | Free tier |

**Trade-offs:**
- Cloud: easier setup, no hardware, lower upfront
- Local: more private, cheaper long-term, more control

For students: start with cloud, migrate to local as you scale.
