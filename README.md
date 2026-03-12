# O-Camper ESPHome Controller
A modular ESP32-powered control and display system for campers. This isnt much different than a lot of camper controllers people have built, but I do use ESPNow to sync with a remote LCD.

The O-Camper project is a custom ESPHome-based control system designed for campers. It provides a clean, responsive web interface, synchronized fan and lighting control, and a flexible communication layer between the camper and a remote monitoring devices (see my other projects).
This repository contains the firmware for the main camper controller.

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
