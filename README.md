# Trajectory Labs Office Robot

Weekend hackathon project — a cool, interactive AI robot for the office. Should feel like a character, not a tool.

## Interaction Modes

- **Voice** — natural turn-taking conversation with a fun personality
- **Slack** — text the robot, get texts back, summon it to locations
- **Ambient** — sees things and comments unprompted, greets people

## MVP Requirements

- Drive around the office and navigate to locations
- Natural voice conversations
- Camera vision — LLM decides actions based on what it sees
- Ambient awareness (unprompted comments)
- Slack integration (send/receive messages, respond to summons)
- Obstacle avoidance
- Has a face/character

## Constraints

- **Size:** 50cm–1m tall, needs a face
- **Budget:** Under $300 CAD (see `parts.md`)
- **Timeline:** Weekend hackathon (2 days), 4-6+ people
- **Skills:** Python confirmed, 3D printing confirmed, electronics uncertain
- **Environment:** Indoor coworking space — main room, hallways, meeting rooms. Not huge. Robot can't open doors (asks humans via Slack).

## Architecture (Settled)

- **Raspberry Pi 4** as main computer
- **Camera + speaker + mic** for perception and voice
- **LLM via cloud API** (Claude/OpenAI) as the brain
- **Two-loop design:** fast motor control loop on Pi (~20-50Hz, no LLM) + slow thinking loop (camera + status → LLM every few seconds → tool calls)
- **Inner monologue pattern:** LLM continuously receives camera frames, nav status, Slack messages, and decides what to do next
- **Power:** Separate supplies for Pi (USB power bank) and motors (AA batteries) to avoid voltage dip crashes

## Open Decisions

### Navigation
1. Remote control only
2. Remote control + obstacle avoidance sensors
3. Pre-mapped waypoints with odometry (drift issues)
4. Pre-mapped waypoints with LIDAR (~$25-100, much more accurate)
5. Full SLAM with ROS2/Nav2 (most robust, most complex)

### Agent Architecture
1. Simple script — LLM only for conversation, no autonomy
2. Custom agent loop — Python script, LLM responds with tool calls like `navigate_to("kitchen")`
3. OpenClaw integration — free Slack/memory/Claude support, but not built for hardware control
4. Full ROS2 + LLM planner — industry-standard, heaviest setup

### Voice
1. Basic offline TTS + text input only
2. Offline STT → LLM → offline TTS (clunky, noticeable pause)
3. Cloud STT → LLM → cloud TTS (better quality, still sequential)
4. Streaming voice API e.g. OpenAI Realtime (~$0.06/min, most natural)

### Physical Form
1. Bare chassis — functional, no personality
2. Tall pole on wheels — camera/speaker up high
3. Friendly character robot — 3D-printed body with screen/LED face

## Open Questions

- **WiFi reliability** — cloud-dependent robot + dead spots = problems. Fallback behavior needed?
- **Safety behaviors** — what happens when stuck, low battery, lost WiFi?
- **Battery life** — USB power bank gives ~6-10hrs for Pi, AAs last longer for motors. Consider a "home base" charging spot.
