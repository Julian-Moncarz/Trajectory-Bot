# Trajectory Labs Office Robot

Let's make a robot body, and have an agent running on a laptop pilot it!

The MVP here is just: wire components up to a Pi Zero on a frame, expose them, have Claude Code on a laptop pilot the bot via curl.
Uses:
- You can talk to the robot body and have a real-time convo with it
- The robot can see things or people and comment unprompted
- Slack - you can text the robot, get texts back, ask it to come to locations

Environment: Indoor coworking space — main room, hallways, meeting rooms. Same env all the time. Robot can't open doors (asks humans via Slack or something).

## Architecture

- **Onboard Pi Zero** thin router - just exposes utilities over HTTP
- **Laptop** running a Claude Code session or something as the brain and pilot
