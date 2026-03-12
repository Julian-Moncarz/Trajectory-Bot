# Trajectory Labs Office Robot

Let's make a robot body, and have an agent running on a laptop pilot it!

Uses:
- You can talk to the robot body and have a realtime convo with it
- The robot can see things or people and comment unprompted
- Slack - you can text the robot, get texts back, ask it to come to locations

Environment: Indoor coworking space — main room, hallways, meeting rooms. Same env all the time. Robot can't open doors (asks humans via Slack or something).

## Architecture

- **Onboard pi 0** thin router - just exposes utilities over http
- **Laptop** running a claude code session or something as the brain and pilot
