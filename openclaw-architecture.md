# OpenClaw + Thin Body Architecture

## Idea

OpenClaw agent runs on an old laptop (or any always-on machine) in the office. The robot body is just a session — a thin client that sends sensor data up and receives commands back.

## How it works

```
[Robot Body (Pi Zero 2 W)]          [Office Laptop]
  Camera frames ──────────────►
  Mic audio     ──────────────►     OpenClaw Agent
  Motor status  ──────────────►       - LLM brain
                                      - Slack integration
  Motor commands ◄────────────        - Memory
  Audio to play  ◄────────────        - Decision-making
```

The robot body is dumb. It captures camera/mic, streams them to the laptop over WiFi, and executes whatever motor/audio commands come back. All intelligence lives in OpenClaw.

## What runs where

### On the robot (Pi Zero 2 W)
- Camera capture + stream frames over WiFi
- Mic audio capture + stream over WiFi
- Receive + play audio through speaker
- Motor control loop (fast, local, ~20-50Hz)
- Receive navigation/motor commands over WiFi

### On the laptop (OpenClaw)
- LLM calls (Claude/OpenAI)
- Slack integration (built into OpenClaw)
- Persistent memory (built into OpenClaw)
- Vision processing (receives camera frames, sends to LLM)
- Decision-making ("go to kitchen", "say hello", "message Slack")
- Voice processing (STT/TTS via cloud APIs)

## Why this is interesting

- OpenClaw already has Slack integration, memory, and Claude tool use — don't have to build any of that
- Pi Zero 2 W is $22 vs Pi 4 at $85 — saves ~$63
- Laptop can be way more powerful than any Pi — no compute constraints on the agent
- Robot body becomes a replaceable peripheral — can iterate hardware without touching the agent
- Could run multiple "bodies" from one agent (stretch goal)

## Concerns

- **WiFi dependency is total** — if WiFi drops, robot is completely brain-dead (not just slow)
- **Camera streaming latency** — need to figure out efficient frame transport (MJPEG? WebSocket? RTSP?)
- **Audio latency** — round-trip for voice conversation goes: mic → WiFi → laptop → cloud STT → LLM → cloud TTS → laptop → WiFi → speaker. More hops = more delay
- **Two machines to debug** during hackathon instead of one
- **Laptop needs to stay on** — if someone closes it or it sleeps, robot dies

## Open questions

- What protocol for camera streaming? (MJPEG over HTTP is simplest)
- What protocol for motor commands? (WebSocket? MQTT? Simple HTTP?)
- Does OpenClaw have a concept of "sessions" that maps to this, or do we need to build the body-agent bridge?
- Can we get a dedicated old laptop for this, or use someone's machine?
