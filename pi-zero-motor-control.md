# Pi Zero 2 W: On-Board Capabilities & Motor Control

## What the Pi Zero 2 W can handle

Quad-core ARM Cortex-A53 @ 1GHz, 512MB RAM, 2.4GHz WiFi.

**Yes:**
- Motor control loop (20-50Hz GPIO + PWM)
- Ultrasonic sensor reading (HC-SR04)
- Reactive obstacle avoidance ("if distance < 30cm, stop")
- Pre-mapped waypoint navigation with A* (small grid, <1ms)
- Odometry / PID for straight-line driving
- MJPEG camera streaming (640x480 ~15fps)
- Mic/speaker audio capture + playback
- Lightweight HTTP server (FastAPI/Flask)

**No:**
- Any LLM inference (512MB RAM, no NPU)
- On-device STT/TTS (Whisper-tiny would be 5-10s per utterance)
- Heavy CV (YOLO, SLAM, object detection)
- ROS2 + Nav2 (needs 1-2GB RAM)

## Architecture split

- **Laptop decides *where* to go** → `POST /navigate {"location": "kitchen"}`
- **Pi figures out *how* to get there** → local A*, motor control, obstacle avoidance
- **Pi reports back** → "arrived", "blocked", "lost"

If WiFi blips mid-navigation, the robot keeps driving its current path.

## Motor control code

L298N motor driver wired to GPIO pins. Using `gpiozero` (preinstalled on Raspberry Pi OS).

```python
from fastapi import FastAPI
from gpiozero import Motor
import uvicorn

app = FastAPI()

# GPIO pin numbers — adjust to match your wiring
left = Motor(forward=17, backward=18, enable=12)
right = Motor(forward=22, backward=23, enable=13)

@app.post("/drive")
def drive(command: str, speed: float = 0.7):
    """command: forward, backward, left, right, stop"""
    actions = {
        "forward":  lambda: (left.forward(speed), right.forward(speed)),
        "backward": lambda: (left.backward(speed), right.backward(speed)),
        "left":     lambda: (left.backward(speed), right.forward(speed)),
        "right":    lambda: (left.forward(speed), right.backward(speed)),
        "stop":     lambda: (left.stop(), right.stop()),
    }
    if command not in actions:
        return {"error": f"unknown command: {command}"}
    actions[command]()
    return {"status": "ok", "command": command}

@app.post("/navigate")
def navigate(location: str):
    """Go to a named waypoint using local A* + motor control."""
    # TODO: waypoint lookup, A* pathfinding, execute path
    pass

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

## LLM tool definition

The laptop agent gets this as a tool:

```json
{
  "name": "drive",
  "description": "Control the robot's movement",
  "parameters": {
    "command": "forward | backward | left | right | stop",
    "speed": "0.0 to 1.0"
  }
}
```

Agent code translates tool calls into HTTP requests to `http://<pi-ip>:8000/drive`.

## Camera (Pi Camera Module 3)

Ribbon cable into the Pi Zero's CSI port. **Needs a Pi Zero camera ribbon cable** (~$3) — the Zero has a smaller CSI connector than the Pi 4.

Stream MJPEG to the laptop:

```bash
# Simplest — built-in libcamera HTTP streaming
libcamera-vid -t 0 --width 640 --height 480 --framerate 15 --codec mjpeg --listen -o tcp://0.0.0.0:8080
```

Or via the FastAPI server for more control:

```python
from picamera2 import Picamera2
import io, time
from fastapi.responses import StreamingResponse

cam = Picamera2()
cam.configure(cam.create_video_configuration(main={"size": (640, 480)}))
cam.start()

@app.get("/camera")
def stream():
    def generate():
        while True:
            buf = io.BytesIO()
            cam.capture_file(buf, format="jpeg")
            frame = buf.getvalue()
            yield (b"--frame\r\nContent-Type: image/jpeg\r\n\r\n" + frame + b"\r\n")
            time.sleep(0.066)  # ~15fps
    return StreamingResponse(generate(), media_type="multipart/x-mixed-replace; boundary=frame")
```

## Speaker + Mic (USB)

Pi Zero 2 W has one micro-USB data port. Need a **micro-USB OTG hub** (~$8) to plug in both USB mic and USB speaker. Or get a combo USB audio adapter with mic-in + speaker-out (~$8, saves a port).

They show up as ALSA audio devices automatically — no drivers needed.

```python
import subprocess
# Playback
subprocess.run(["aplay", "-D", "plughw:1,0", "audio.wav"])
# Recording
subprocess.run(["arecord", "-D", "plughw:2,0", "-f", "S16_LE", "-r", "16000", "recording.wav"])
```

For streaming to/from the laptop: pipe raw audio over WebSocket or send WAV chunks via HTTP.

## Face Display (3.5" LCD, SPI)

480x320 color LCD. Plugs onto the GPIO header via SPI — no USB port used. Big enough for expressive animated faces visible across a room.

Uses GPIO pins 8, 9, 10, 11, 24, 25 (SPI) — no conflict with motor or sensor pins.

```python
import pygame
pygame.init()
screen = pygame.display.set_mode((480, 320))

# Draw face
pygame.draw.circle(screen, (255,255,255), (160, 120), 30)  # left eye
pygame.draw.circle(screen, (255,255,255), (320, 120), 30)  # right eye
pygame.display.flip()
# Animate blinking, reactions, expressions, etc.
```

## GPIO pin budget

| Peripheral | Pins used |
|-----------|-----------|
| L298N motors | 6 GPIO |
| HC-SR04 ultrasonic | 2 GPIO (trigger + echo) |
| Camera | CSI ribbon (dedicated port, no GPIO) |
| 3.5" LCD | SPI (GPIO 8,9,10,11,24,25) |
| USB mic + speaker | Micro-USB OTG hub (no GPIO) |

14 of 26 usable GPIO pins used. Plenty of room.
