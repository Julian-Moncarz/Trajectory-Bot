# Hackathon Day Plan (2-3 people, 8 hrs)

**Goal:** Robot drives, sees, talks, has a face. LLM on laptop controls it via camera frames.

## Pre-work (before the day)
- Flash Pi OS Lite, enable SSH, configure WiFi
- Install: `pip install fastapi uvicorn picamera2 gpiozero pygame`
- 3D print body/mounts
- Test LCD driver setup (this one eats time)

## Phases (parallelize across people)

| Phase | Time | What | Done when |
|-------|------|------|-----------|
| 1. Chassis | 1.5h | Assemble chassis, wire L298N + motors, run FastAPI motor server | `curl /drive forward` makes it move |
| 2. Camera + audio | 1.5h | Ribbon cable, MJPEG stream, mic + speaker via OTG hub | Laptop sees + hears the robot |
| 3. Face | 1.5h | LCD + pygame face with expressions, `POST /face` API | Can change face via curl |
| 4. LLM brain | 2-3h | Laptop script: grab frame → Claude → tool calls (drive, face, say) | Robot looks around and reacts |
| 5. Voice | 1-2h | Mic → STT (Whisper API) → LLM → TTS (ElevenLabs) → speaker | Can talk to the robot |
| 6. Polish | 1h | Personality tuning, cable management, demo video | |

Phases 1, 2, 3 run in parallel. Phase 4 starts once 1+2 work. Phase 5 after 4 works.

## If stuck
- LCD not working after 45 min → fall back to OLED or skip face
- Audio device confusion → hardcode ALSA device IDs
- WiFi dropping → USB WiFi dongle or move closer to router
- **No scope creep:** no Slack, no waypoints, no obstacle avoidance on day 1
