# Parts List (Amazon.ca)

All prices in CAD. Order from Amazon.ca for 1-2 day Prime delivery.

## Core Computing

| Part | Est. Price | Link |
|------|-----------|------|
| Raspberry Pi Zero 2 W | $25 | [Amazon.ca](https://www.amazon.ca/s?k=raspberry+pi+zero+2+w) |
| MicroSD card (32GB) | $10 | [Amazon.ca](https://www.amazon.ca/s?k=32gb+micro+sd+card) |
| Spare MicroSD card (32GB, pre-flash before hackathon) | $10 | [Amazon.ca](https://www.amazon.ca/s?k=32gb+micro+sd+card) |
| Pi Zero camera ribbon cable (smaller CSI connector) | $3 | [Amazon.ca](https://www.amazon.ca/s?k=pi+zero+camera+cable) |
| Micro-USB OTG hub (for USB audio) | $8 | [Amazon.ca](https://www.amazon.ca/s?k=micro+usb+otg+hub) |
| Mini-HDMI to HDMI adapter (emergency debug) | $5 | [Amazon.ca](https://www.amazon.ca/s?k=mini+hdmi+to+hdmi+adapter) |

## Networking

Use Tailscale (free) on both laptop and Pi. Creates a direct WireGuard tunnel so they can reach each other regardless of coworking WiFi client isolation. Install + configure before hackathon day.

## Vision & Audio

| Part | Est. Price | Link |
|------|-----------|------|
| Raspberry Pi Camera Module 3 | $40 | [Amazon.ca](https://www.amazon.ca/Raspberry-Pi-Camera-Module/dp/B0BRY6MVXL) |
| Spare camera ribbon cable | $3 | [Amazon.ca](https://www.amazon.ca/s?k=pi+zero+camera+cable) |
| USB audio adapter (mic-in + speaker-out, single device) | $10 | [Amazon.ca](https://www.amazon.ca/s?k=usb+audio+adapter+microphone+speaker) |
| Mini USB microphone | $10 | [Amazon.ca](https://www.amazon.ca/SunFounder-Microphone-Raspberry-Recognition-Software/dp/B01KLRBHGM) |
| Small portable speaker (loud enough for demo day) | $15 | [Amazon.ca](https://www.amazon.ca/s?k=small+portable+speaker+3.5mm) |

## Drive & Motors

| Part | Est. Price | Link |
|------|-----------|------|
| 2WD robot chassis kit (motors + wheels) | $22 | [Amazon.ca](https://www.amazon.ca/DIY-Smart-Robot-Chassis-Arduino/dp/B01N7KJIW4) |
| L298N motor driver board | $15 | [Amazon.ca](https://www.amazon.ca/l298n-motor-driver/s?k=l298n+motor+driver) |

## Sensors & Display

| Part | Est. Price | Link |
|------|-----------|------|
| HC-SR04P ultrasonic sensor (3.3V version!) | $6 | [Amazon.ca](https://www.amazon.ca/s?k=HC-SR04P+ultrasonic+sensor+3.3v) |
| Logic level shifter (3.3V/5V, in case any sensor is 5V) | $5 | [Amazon.ca](https://www.amazon.ca/s?k=logic+level+shifter+3.3v+5v) |
| Small display for face (OLED) | $20 | [Amazon.ca](https://www.amazon.ca/s?k=large+OLED) |

## Power

| Part | Est. Price | Link |
|------|-----------|------|
| 10,000mAh power bank with micro-USB output (for Pi) | $20 | [Amazon.ca](https://www.amazon.ca/s?k=10000mah+power+bank+micro+usb) |
| USB keep-alive dongle (prevents power bank auto-shutoff) | $3 | [Amazon.ca](https://www.amazon.ca/s?k=usb+keep+alive+dongle) |
| 4x AA NiMH rechargeable batteries + charger (for motors) | $18 | [Amazon.ca](https://www.amazon.ca/s?k=4+AA+rechargeable+batteries+charger) |
| 4-slot AA battery holder | $3 | [Amazon.ca](https://www.amazon.ca/s?k=4+AA+battery+holder) |

## Wiring & Mounting

| Part | Est. Price | Link |
|------|-----------|------|
| Jumper wires + breadboard kit | $12 | [Amazon.ca](https://www.amazon.ca/s?k=breadboard+jumper+wire+kit) |
| Mounting hardware (standoffs, screws, zip ties) | $8 | [Amazon.ca](https://www.amazon.ca/s?k=M2.5+standoff+kit+raspberry+pi) |
| Velcro strips (rapid mount/remount during iteration) | $5 | [Amazon.ca](https://www.amazon.ca/s?k=velcro+strips+adhesive) |
| Hot glue gun + sticks (secure vibration-prone connections) | $10 | [Amazon.ca](https://www.amazon.ca/s?k=mini+hot+glue+gun) |

## Total

| | |
|--|--|
| **Estimated total** | **~$285 CAD** |

## Notes

- **Architecture:** Pi Zero 2 W is a thin HTTP router exposing hardware. Laptop runs Claude Code as the brain and pilots via curl.
- Separate power for Pi (power bank) and motors (AA batteries) — avoids voltage dips crashing the Pi
- **HC-SR04 vs HC-SR04P:** The standard HC-SR04 is 5V and WILL damage Pi Zero GPIO (3.3V). Get the HC-SR04P (3.3V compatible) or use a level shifter.
- Some chassis kits include an L298N — check listing to avoid buying it twice
- **Pre-hackathon:** flash both SD cards, set up WiFi config, test SSH over the travel router. Don't do first-time setup on hackathon day.
- Bring: a keyboard (for emergency Pi debug), laptop HDMI cable, extension cord, power strip
