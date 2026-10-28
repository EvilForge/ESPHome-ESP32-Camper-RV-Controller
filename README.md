# O-Camper ESPHome Controller
A modular ESP32-powered control and display system for campers. This isnt much different than a lot of camper controllers people have built, but I do use ESPNow to sync with a remote LCD.

The O-Camper project is a custom ESPHome-based control system designed for campers. It provides a clean, responsive web interface, synchronized fan and lighting control, and a flexible communication layer between the camper and a remote monitoring devices (see my other projects).
This repository contains the firmware for the main camper controller.

## Note - 10-28-2026
This works well.. but there are issues with ESPNow reaching my remote management screens, and you have to have them powered. Plus its just one more thing to carry around when youre trying to get away from things (I mean, its camping right?)  

So, I returned to the idea of a BLE stack only controller and some sort of phone client. My previous attempts to get BLE working with ESPHome were difficult.. the BLE stack isnt intuitive. BUT, with a lot of help from Claude, I've rebuilt a new system using a ESP32 S3 N16R8 dev board (its huge but it has an external antenna, which was a major improvement). The ESP runs native Arduino code and only speaks BLE - so it turns out that Home assistant totally understands and picks it up via BT Proxies, and the Android app Claude helped be co-develop works pretty well.. I did rebuild the hardware of course, but it plugs in pretty much the same, and its just 'simpler' all around. And less power. With lower polling rates the controller idles at 23mA and spikes to 30mA.

I'm taking this out for a test camping trip soon and will push contents up when I'm satisfied. So, for now, this repo and the round LCD remotes are obsolete, but I haven't pushed the replacement repos up yet.

## Features
### Fan Control
- Variable-speed PWM fan output
- Remote fan speed sync via packet_transport
- Smooth speed adjustments in 0–100% increments
### Lighting Control
- Monochromatic LED dimming via PWM
- Remote brightness sync
- UI feedback for current light level
### Packet Transport Communication
- ESPHome-native messaging between nodes
- Screen node receives live updates from main controller
- No MQTT or external cloud required
### Hardware-Friendly
- ESP32 DevKit or equivalent
- LEDC PWM outputs for fan and lighting

Remote Sensor Integration (The Remote is NOT the subject of this repo. This is just for additional info..)
The screen device receives real-time values from the main controller using ESPHome’s packet_transport (example):
sensor:
  - platform: packet_transport
    provider: o-camper
    transport_id: ptrans_camper_to_screen
    id: remote_fan
    remote_id: inside_fan_template
    name: "Inside Fan"

This exposes the fan speed as a 0–100% float, which the UI displays. However... since ESPHome has some quirks in how fan speed and light levels work (you'd think setting the levels to 0 would turn them off? Nope!), the code also sends the fan and light state as 0 or 1 (off or on), so a tap in the middle of the remote screen toggles the item.

## Fan & Light Control Logic
Both the fan and light are driven by LEDC PWM outputs:
fan:
  - platform: speed
    id: inside_fan
    output: fan_pwm

light:
  - platform: monochromatic
    id: area_light
    output: light_pwm

## Requirements
- ESPHome 
- PWM-capable fan and LED driver

## Getting Started (if youre using this and the lcd remote node)
- Clone this repository.
- Add your Wi-Fi credentials to secrets.yaml, and set up your mac addresses and passwords. (look for REDACTED in the code)
- Flash the remote screen device firmware.
- Flash the main controller firmware.
- Reboot both devices.
- Verify that they auto-sync via packet transport.
