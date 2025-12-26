---
title:  My Home Assistant Devices
description: A running list of my Home Assistant devices.
date: 2025-12-26
tags: home assistant
---

A running list of my devices that are connected to Home Assistant. 

## Overview
- x Wi-Fi devices
  - 4 ESPHome devices
  - 2 WLED devices
  - 3 Matter-over-WiFi devices
- 3 Matter-over-Thread devices

## Devices 
### ESPHome 
#### Apollo MSR-2 mmWave Multisensor 2x
I ordered two of [these sensors](https://apolloautomation.com/products/msr-2) to detect presence in zones in my living room/kitchen area and master bedroom/bathroom. The mmWave detection makes it possible to measure distance from the sensor and define 3 zones, which I use to differentiate between presence in the kitchen vs. couch/desk area and turn on different lights (I have it placed in the corner of the room on the fridge). This sensor is purportedly better at detecting still objects than the [Apollo MTR-1 multi-target multisensor](https://apolloautomation.com/products/mtr-1), but it tends to clear my presence once I have been still for a few minutes. It works better when multiple people are present. I increased the timeout of my automations to 10 minutes to prevent the lights from turning off when I'm still there. 

#### ESP8285 with IR Transmitter 2x 
I got these [microcontrollers](https://a.co/d/bRTAfnt) to control my Mitsubishi mini-split heat pump units. I paired them with the [CP2102 USB to TTL converter](https://a.co/d/bqQEWH5) for programming and power and installed ESPHome with the [IR Remote Climate](https://esphome.io/components/climate/climate_ir/) component. These units are not as powerful as ESP32s and don't have pins for connecting to additional sensors, but the built-in IR transmitter made it simple to set them up and keep the package relatively neat. If someone makes an ESP32 version with built-in IR transmitter, temperature sensor, and motion sensor I would probably upgrade.  

<details>
  <summary>ESPHome Configuration</summary>
    <pre><code>
    esphome:  
    name: esphome-web-f81aef
    friendly_name: Heat Pump 1
    min_version: 2025.9.0
    name_add_mac_suffix: false

    esp8266:
    board: esp8285

    # Enable logging
    logger:

    # Enable Home Assistant API
    api:

    # Allow Over-The-Air updates
    ota:
    - platform: esphome

    # Import Fan Temperature from HA
    sensor:
    - platform: homeassistant
    name: "fan temperature"
    entity_id: sensor.fan_temperature
    id: fan_temperature
    internal: True
    filters:
        - lambda: return (x - 32.0) * 5.0 / 9.0;

    wifi:
    ssid: !secret wifi_ssid
    password: !secret wifi_password

    remote_transmitter:
    pin: GPIO4
    # Infrared remotes use a 50% carrier signal
    carrier_duty_percent: 50%

    climate:
    platform: mitsubishi
    name: "Bedroom Heat Pump"
    sensor: fan_temperature
    <pre></code>
</details>

### WLED
#### Adafruit Matrix Portal S3 with 32x32 HUB75 Matrix

#### SK6812RGBW LED Strip with ESP8266

### Matter-over-Thread
#### Nanoleaf Essentials Matter A19 E26 Smart Bulb

### Matter-over-WiFi 
#### GE Cync Reveal Undercabinet Bar Lights 2x

#### SwitchBot Candle Warmer Lamp

### Other Wi-Fi
#### Bread Lamp Dimmer

#### Dreo TurboPoly Fan 704S

#### Nanoleaf Multicolor Floor Lamp

#### Nanoleaf Shapes Triangles

#### Mi Desk Lamp 1S

#### HASS.Agent on Windows 11 Laptop

#### Blueair Blue Pure 411i Max 

#### GE Washer & Dryer 