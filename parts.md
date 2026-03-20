# Parts List (Amazon.ca)

All prices in CAD. Order from Amazon.ca for 1-2 day Prime delivery.

## Core Computing

| Part | Est. Price | Link |
|------|-----------|------|
| Raspberry Pi Zero 2 W (WITH pre-soldered headers) | $30 | [Amazon.ca](https://www.amazon.ca/s?k=raspberry+pi+zero+2+w+pre+soldered+headers) |
| MicroSD card (32GB) | $10 | [Amazon.ca](https://www.amazon.ca/s?k=32gb+micro+sd+card) |
| Spare MicroSD card (32GB, pre-flash before hackathon) | $10 | [Amazon.ca](https://www.amazon.ca/s?k=32gb+micro+sd+card) |
| Pi Zero camera ribbon cable (smaller CSI connector) | $3 | [Amazon.ca](https://www.amazon.ca/s?k=pi+zero+camera+cable) |
| Mini-HDMI to HDMI adapter (emergency debug) | $5 | [Amazon.ca](https://www.amazon.ca/s?k=mini+hdmi+to+hdmi+adapter) |
| Pi Zero heatsink (stick-on) | $4 | [Amazon.ca](https://www.amazon.ca/s?k=pi+zero+heatsink) |

## Networking

Use Tailscale (free) on both laptop and Pi. Creates a direct WireGuard tunnel so they can reach each other regardless of coworking WiFi client isolation. Install + configure before hackathon day.

## Vision & Audio

| Part | Est. Price | Link |
|------|-----------|------|
| Raspberry Pi Camera Module 3 | $40 | [Amazon.ca](https://www.amazon.ca/Raspberry-Pi-Camera-Module/dp/B0BRY6MVXL) |
| Spare camera ribbon cable | $3 | [Amazon.ca](https://www.amazon.ca/s?k=pi+zero+camera+cable) |
| PCM5102 I2S DAC module (audio out via GPIO, no USB needed) | $10 | [Amazon.ca](https://www.amazon.ca/s?k=PCM5102+I2S+DAC+raspberry+pi) |
| Mini USB microphone (plugs directly into Pi's USB port) | $10 | [Amazon.ca](https://www.amazon.ca/SunFounder-Microphone-Raspberry-Recognition-Software/dp/B01KLRBHGM) |
| Small active speaker with 3.5mm aux input + own battery | $15 | [Amazon.ca](https://www.amazon.ca/s?k=small+portable+speaker+3.5mm+aux+input+rechargeable) |

## Drive & Motors

| Part | Est. Price | Link |
|------|-----------|------|
| 2WD robot chassis kit (motors + wheels) | $22 | [Amazon.ca](https://www.amazon.ca/DIY-Smart-Robot-Chassis-Arduino/dp/B01N7KJIW4) |
| TB6612FNG motor driver (3.3V logic, <0.5V drop) | $8 | [Amazon.ca](https://www.amazon.ca/s?k=TB6612FNG+motor+driver) |

## Sensors & Display

| Part | Est. Price | Link |
|------|-----------|------|
| VL53L0X Time-of-Flight distance sensor (I2C, 3.3V native) | $6 | [Amazon.ca](https://www.amazon.ca/s?k=VL53L0X+time+of+flight+sensor) |
| 1.3" OLED display, I2C, SH1106 (for face) | $15 | [Amazon.ca](https://www.amazon.ca/s?k=1.3+inch+OLED+I2C+SH1106) |

## Power

| Part | Est. Price | Link |
|------|-----------|------|
| 10,000mAh power bank with micro-USB output (for Pi) | $20 | [Amazon.ca](https://www.amazon.ca/s?k=10000mah+power+bank+micro+usb) |
| USB keep-alive dongle (prevents power bank auto-shutoff) | $3 | [Amazon.ca](https://www.amazon.ca/s?k=usb+keep+alive+dongle) |
| 6x AA NiMH rechargeable batteries + charger (for motors) | $20 | [Amazon.ca](https://www.amazon.ca/s?k=6+AA+rechargeable+batteries+charger) |
| 6-slot AA battery holder | $3 | [Amazon.ca](https://www.amazon.ca/s?k=6+AA+battery+holder) |
| Micro-USB cable (power bank → Pi) | $3 | [Amazon.ca](https://www.amazon.ca/s?k=short+micro+usb+cable) |

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
| **Estimated total** | **~$255 CAD** |

## Notes

- **Architecture:** Pi Zero 2 W is a thin HTTP router exposing hardware. Laptop runs Claude Code as the brain and pilots via curl.
- Separate power for Pi (power bank) and motors (AA batteries) — avoids voltage dips crashing the Pi
- **VL53L0X vs HC-SR04P:** VL53L0X is a laser time-of-flight sensor. Uses I2C (shared bus, fewer GPIO pins), does ranging internally (no timing-sensitive code on Linux), natively 3.3V. 1.2m max range is plenty indoors. If unavailable on Amazon, fall back to HC-SR04P ($6) + logic level shifter ($5).
- **PCM5102 I2S DAC vs USB audio adapter:** The DAC connects directly to GPIO for audio output — no USB port or OTG hub needed. Fewer cables, fewer failure points on a moving robot. If unavailable, fall back to USB audio adapter ($10) + OTG hub ($8) + 3.5mm clip-on mic ($5).
- Some chassis kits include a motor driver — check listing to avoid buying it twice
- **TB6612FNG vs L298N:** The TB6612 is a MOSFET driver with <0.5V voltage drop (vs ~2V on L298N). With 6xAA (7.2V) you get ~6.7V to motors instead of ~5.2V. Also natively 3.3V logic — no level shifting needed.
- **Pre-hackathon:** flash both SD cards, set up WiFi config, install Tailscale, write + test Pi HTTP server. Don't do first-time setup OR first-time coding on hackathon day.
- **Bring from home:** wire strippers, small Phillips screwdriver, keyboard (emergency Pi debug), HDMI cable, extension cord, power strip, spare micro-USB cables, multimeter if you own one
