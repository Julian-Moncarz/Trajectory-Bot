# Trajectory Labs Office Robot

Fun demo and team-building hackathon project for Trajectory Labs. Not solving a specific problem — it's about having a cool, interactive AI presence in the office. Should feel impressive enough that people genuinely want to interact with it. Built during a weekend hackathon with ongoing iteration afterward.

## Users & Interactions

Users are Trajectory Labs members. Three interaction modes:

- **Voice conversation** — high quality, natural turn-taking, with a fun voice
- **Slack messaging** — text the robot, robot texts back, robot can be summoned
- **Passive/ambient** — robot sees things and comments unprompted, greets people, has personality

The robot should feel like a character, not a tool.

## Environment

Indoor coworking office space. Multiple rooms with doors (robot doesn't need to open doors — can ask humans or message Slack). People walking around, so it needs to be visible enough not to get stepped on. Stretch goal: elevator access for speaker events (~2x/week). **Office layout:** One main room (island table, sink, coffee, big lunch table, nooks) accessed through push/pull doors from elevators, plus hallways leading to meeting rooms and office rooms. Not a huge space — navigation should be manageable.

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

- Under $300 CAD hardware
- Weekend hackathon (2 days), 4-6+ people
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

## Things to figure out:

WiFi reliability — the robot depends on cloud APIs for its brain. What's the WiFi like throughout the office? Dead spots = a robot that suddenly goes braindead mid-hallway. Does it need a fallback behavior when it loses connection? **Safety behaviors:** Need to define what happens when stuck, low battery, lost WiFi, hears nothing for a while. Important but not yet specified

## Battery:

Got it. Let me research the battery/charging situation for a Pi 5-based mobile robot, then I'll summarize everything including the new context you gave me.Here's what I found on battery and charging for this robot. Let me break it down clearly.

### The core problem

The Pi 5 is power-hungry compared to older Pis. It idles at ~4W and peaks around 25W under heavy load. For our robot, realistic average draw would be somewhere around 8-12W when you add up the Pi (doing WiFi + camera + processing), motors (intermittent but spiky), speaker, mic, and camera. Motors are the wild card — they draw very little when cruising but huge spikes when starting up or hitting resistance (like carpet transitions or bumping into things).

### The key design decision: one battery or two?

The strong consensus from the robotics community is **use separate power for the Pi and the motors**. When motors start up, they create current spikes that can cause the Pi's voltage to dip, which either crashes it or corrupts the SD card. Commercial robots like Roombas solve this with proper power engineering, but for a weekend hackathon, two separate supplies is much safer and simpler.

**Recommended approach:** One power source for the Pi (brain), one for the motors (muscle). They share a common ground but otherwise stay isolated.

### Option 1: USB power bank for Pi + AA batteries for motors (~$25-40)

This is the simplest, cheapest approach. A 20,000mAh USB-C power bank powers the Pi, and a pack of 6-8 rechargeable AA NiMH batteries powers the motors through the motor driver.

**Runtime estimate:** A decent 20,000mAh power bank gives roughly 50-70Wh usable. At ~6-8W average Pi draw, that's **6-10 hours for the Pi**. The AA pack for motors would last longer than that for small DC motors that aren't running continuously.

**Pros:** Cheap, no soldering, swap/recharge power bank easily, everyone has a phone charger to recharge it.
**Cons:** Power bank might not deliver the full 5A the Pi 5 wants (most cap at 3A, which actually works fine for most use cases). Bulky — you're carrying a phone power bank inside the robot. Some power banks auto-shut-off if current draw is too low (Pi at idle might trigger this).

**Gotcha with Pi 5 specifically:** The Pi 5 uses USB Power Delivery (PD) negotiation to unlock its full 5A mode. Most cheap power banks don't support PD properly, so the Pi runs in a limited 600mA USB mode. In practice, people report 3A power banks run the Pi 5 fine for most tasks — you just can't plug in tons of USB peripherals. For our use case (camera, mic, speaker) it should be okay.

### Option 2: UPS HAT with 18650 batteries (~$30-55 + $15-30 for batteries)

A UPS HAT (an add-on board that sits on top of or below the Pi) holds rechargeable 18650 lithium cells and provides proper regulated 5V/5A power to the Pi. Popular options include the Geekworm X1200 (2-cell, ~$30) or X1202 (4-cell, ~$50), and the DFRobot UPS HAT (~$53).

**Runtime estimate with 4x 18650 cells:** Four quality 3000mAh 18650s give about 44Wh. At ~6-8W Pi draw, that's roughly **5-7 hours**. The DFRobot product page claims up to 10 hours with good batteries. Motors would still need their own separate battery pack.

**Pros:** Clean, purpose-built solution. Charges via USB-C or DC jack while running (you can plug it in and it charges the batteries while powering the Pi — true UPS behavior). Provides the full 5A the Pi 5 wants. Some have I2C battery monitoring so the robot can know its own battery level. Compact — sits right on the Pi.
**Cons:** More expensive. Need to buy 18650 batteries separately ($5-8 each for good Samsung/Panasonic cells). Still need separate motor power.

### Option 3: Single LiPo battery with buck converters (~$30-50)

Use one larger RC hobby LiPo battery (e.g., 3S 11.1V, 2200-5000mAh) and two separate buck converters (voltage step-down boards, ~$2-5 each) — one outputting 5V for the Pi, one outputting 6V for the motors. This is how a lot of hobbyist robots are actually built.

**Runtime estimate with 3S 5000mAh LiPo:** About 55Wh total. Split between Pi and motors, maybe **3-5 hours** depending on how much the robot drives.

**Pros:** One battery to charge. Lighter overall. Proper voltage for motors (6-12V is better than 5V for DC motors). Most elegant long-term solution.
**Cons:** Needs a LiPo charger (~$15-20). LiPo batteries need careful handling (can catch fire if punctured or overcharged). Requires some basic wiring/soldering. Slightly more complex.

### Claude's recommendation for the hackathon

**Start with Option 1** (power bank + AA batteries). It's the fastest to get working, requires zero soldering, and gives plenty of runtime for a demo day. Buy a decent 20,000mAh USB-C power bank (~$20-30) and a pack of rechargeable AAs. If the power bank auto-shutoff is a problem, there are tiny USB "keep alive" devices for 3 that prevent it.

**Upgrade to Option 2 or 3 later** if you want a cleaner long-term solution. The UPS HAT is the most "set and forget" option since it charges while running.

**Also strongly consider having a charging cable/dock as a fallback.** Even commercial office robots (like the Amazon Astro) dock to charge. The robot could have a "home base" that's just a spot near an outlet where someone plugs it in, and the robot's personality could even announce "I'm getting tired, heading back to charge."