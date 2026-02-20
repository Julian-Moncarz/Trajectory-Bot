# Trajectory Labs Office Robot

Fun demo and team-building hackathon project for Trajectory Labs. Not solving a specific problem — it's about having a cool, interactive AI presence in the office. Should feel impressive enough that people genuinely want to interact with it. Built during a weekend hackathon with ongoing iteration afterward.

## Users & Interactions

Users are Trajectory Labs members. Three interaction modes:

- **Voice conversation** — high quality, natural turn-taking, with a fun voice
- **Slack messaging** — text the robot, robot texts back, robot can be summoned
- **Passive/ambient** — robot sees things and comments unprompted, greets people, has personality

The robot should feel like a character, not a tool.

## Environment

Indoor coworking office space. Multiple rooms with doors (robot doesn't need to open doors — can ask humans or message Slack). People walking around, so it needs to be visible enough not to get stepped on. Stretch goal: elevator access for speaker events (~2x/week).

## Functional Requirements (MVP — Tier 2)

- Drive around the office
- Navigate to locations when asked
- Natural voice conversations with a fun personality
- Camera vision — LLM makes decisions based on what it sees
- Comments on things unprompted (ambient awareness)
- Slack integration — receives and sends messages, responds to summons
- Obstacle avoidance
- Has a face/character — visual personality matters

## Physical Constraints

50cm to 1m tall. Needs a face. Exact form factor TBD via group planning sprint. Must be buildable in a few hours and hold all electronics. Body design is decoupled from electronics and can be iterated independently.

## Budget, Timeline & Team

- Under $300 hardware
- Weekend hackathon (2 days), 4-6 people
- Python/software skills confirmed
- 3D printing access confirmed
- Electronics/soldering uncertain but figure-outable
- Work should parallelize between physical build and software
- Ongoing iteration post-hackathon

## Technical Decisions — Settled

- **Raspberry Pi 5** as main computer (a small, cheap, credit-card-sized computer that runs Linux)
- **Camera** for vision
- **Speaker + microphone** for voice
- **LLM via cloud API** (Claude and/or OpenAI) as the robot's brain
- **Two-loop architecture:** a fast motor control loop running on the Pi (handles driving, ~20-50 times per second, no LLM involved) + a slow thinking loop (sends camera images and status to the LLM every few seconds, LLM decides what to do next)
- **"Inner Monologue" pattern:** the LLM continuously receives updates — what the camera sees, whether navigation succeeded or failed, recent Slack messages — and makes contextual decisions. For example, it sees a closed door and decides to message Slack asking someone to open it.

## Technical Decisions — Not Yet Settled

### Navigation (how it physically gets around)

1. **Remote control only** — driven via phone/laptop, no autonomy
2. **Remote control + obstacle avoidance** — human drives, but sensors prevent crashes
3. **Pre-mapped waypoints with odometry** — we store a list of named locations as coordinates (e.g. "kitchen is at position 3,7"). The robot tracks its own position by counting wheel rotations (this is called "odometry"). It drives toward the target coordinates. Downside: small errors accumulate over time ("drift"), so the robot gradually loses track of where it really is.
4. **Pre-mapped waypoints with LIDAR** — same as above, but we add a LIDAR sensor (~$25-100), which is a spinning laser that measures distances in all directions 360°. The robot uses this to constantly compare what it sees against a stored map of the office, so it always knows exactly where it is. Much more accurate than odometry alone.
5. **Full SLAM with ROS2/Nav2** — SLAM stands for "Simultaneous Localization and Mapping" — the robot builds its own map of the office while also figuring out where it is on that map, in real time. ROS2 is a widely-used open-source robotics software framework, and Nav2 is its navigation system that handles path planning (finding a route around obstacles), obstacle avoidance, and driving. Most robust, but significant software complexity.

### Agent architecture (how the LLM "brain" works)

1. **Simple script** — hardcoded behavior, LLM only used for conversation when someone talks to it, no autonomous decision-making
2. **Custom agent loop** — a Python script we write ourselves that sends camera images + robot status to the LLM every few seconds. The LLM responds with tool calls like `navigate_to("kitchen")` or `send_slack("Hey, can someone open the door?")`. We control the whole stack and it's easy to understand and modify.
3. **OpenClaw integration** — OpenClaw is a popular open-source AI agent that already has Slack integration, persistent memory, and Claude support built in. We'd get a lot of features for free, but would need to adapt it to control physical hardware (motors, camera, etc.), which it wasn't designed for.
4. **Full ROS2 integration with LLM planner** — the LLM sits on top of a full robotics software stack (ROS2). Most robust and industry-standard, but heaviest learning curve and setup time.

### Voice (how it talks and listens)

1. **Basic TTS + text input** — the robot speaks using offline text-to-speech (a program that converts text to audio on the Pi itself). No voice input — you interact via Slack or typing. Simple but no voice conversation.
2. **Offline STT → LLM → offline TTS** — STT ("speech-to-text") runs locally on the Pi to transcribe what you say, sends the text to Claude, then TTS ("text-to-speech") speaks the response. Works but feels clunky — noticeable pause between you finishing talking and the robot responding, and it can't handle interruptions.
3. **Cloud STT → LLM → cloud TTS** — same pipeline but using faster cloud services (e.g. Deepgram for transcription, ElevenLabs for voice). Better quality and speed, but still a sequential process with a pause while it thinks.
4. **Streaming voice API (e.g. OpenAI Realtime API)** — a single API that handles listening, thinking, and speaking all at once in a continuous stream. Supports natural turn-taking, interruptions, and overlapping speech — feels like talking to a real person. Best quality but most expensive (~$0.06/min) and ties you to a specific provider.

### Physical form factor (what it looks like)

1. **Bare chassis** — electronics mounted on a wheeled platform, no body, ugly but functional
2. **Tall pole on wheels** — vertical tube on wheeled base, camera/speaker near top, minimal personality
3. **Friendly character robot** — 3D-printed or handmade body with a face (screen or LED eyes), gives it real character
4. **Go big** — hoverboard or scooter base for real physical presence, more complex mechanically

## Hard Blocker

Hardware parts need to be ordered ASAP so they arrive in time. The parts list should cover the common components across all options (Pi 5, camera, motors, battery, mic, speaker, motor driver) so choices remain open.
