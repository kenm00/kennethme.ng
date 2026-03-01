---
title:  My Home Assistant Devices
description: A running list of my Home Assistant devices.
date: 2025-12-26
tags: Home Assistant
---

A running list of my devices that are connected to Home Assistant. It would probably make more sense to organize by category (lighting, climate, etc.) but my brain organizes them by communication protocol so I will do that here. 

## Overview
- 18 Wi-Fi devices
  - 4 ESPHome devices
  - 2 WLED devices
  - 3 Matter-over-WiFi devices
  - 1 HomeKit device
  - 8 Other Wi-Fi devices
- 7 Matter-over-Thread devices

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
    </pre></code>
</details>

### WLED
#### Adafruit Matrix Portal S3 with 32x32 HUB75 Matrix
This [microcontroller](https://www.adafruit.com/product/5778) has built-in pins to connect to HUB75 matrix panel (I have [this 32x32 one](https://www.adafruit.com/product/607)). I originally got it to also connect a temperature sensor and IR transmitter to also use it as a thermostat but decided to do that separately (see [ESP8285](#esp8285-with-ir-transmitter-2x) above). Instead I installed [WLED MoonModules](https://mm.kno.wled.ge/), which has support for 2D panels and is a lot easier to control and customize than something I could program using Arduino. It has some pretty cool built-in effects. I still need to make an enclosure for the panel, however.  

#### SK6812RGBW LED Strip with ESP8266
This is an old [RGBW strip](https://www.adafruit.com/product/2828) and [ESP8266 microcontroller](https://www.adafruit.com/product/2821) I had lying around that I originally had manually programmed with Arduino but reflashed with WLED. Final location for this is TBD. 

### Matter-over-Thread
#### Nanoleaf Essentials Matter A19 E26 Smart Bulb 3x
[These](https://us-shop.nanoleaf.me/products/matter-thread-a19-smart-bulbs-3pk) were on sale for a steep discount (~$13 for 3) at Home Depot. They appear to be discontinued now in favor of a Wi-Fi version, but I would prefer my light bulbs to be Thread. They are generally reliable over Thread but do seem to take a beat longer to respond than other lights sometimes. The color quality is decent but the warm whites can look a little bit off.  

#### Philips Hue Essential A19 E26 Smart Bulb 4x
I got a four-pack of [these](https://www.philips-hue.com/en-us/p/hue-white-and-color-ambiance-essential-a19-e26-smart-bulb-800-lm-88w/046677592530) since I was curious about Hue's more affordable option. I have them installed in the bathroom and they are responsive. They do not dim very low, however.  

### Matter-over-WiFi 
#### GE Cync Reveal Undercabinet Bar Lights 2x
This [light](https://www.gelighting.com/smart-home/fixtures/undercabinet) seems to be one of the few Matter-enabled lights designed for undercabinet use (no visible individual LEDs). I have them installed using command strips to my cabinets and the light quality is good (I usually only use the shades of white). I had some issues during setup when one wouldn't add to apple Home but a restart fixed it. They also sometimes have trouble updating their brightness and color temperature fully when Home Assistant sends a commands to update them. I'm not sure if that's an issue with the [adaptive lighting](https://www.gelighting.com/smart-home/fixtures/undercabinet) component in HA, but I have tried various changes to my adaptive lighting configuration and they still exhibit the issue intermittently. Overall I would recommend these (given the limited options for this form factor) if you don't intend on using them with adaptive lighting, but there are more options for strip lights and at least some of them likely have fewer connectivity issues.   

#### SwitchBot Candle Warmer Lamp
I had been thinking about how candles can be made smart when SwitchBot announced this [product](https://us.switch-bot.com/products/switchbot-candle-warmer-lamp?variant=49194196107497). I preordered it the same day and it works as expected with Apple Home and Home Assistant. It appears to throw the scent a bit farther than burning a candle, and puts out a good amount of light too. It uses a 50W halogen bulb which will need to be replaced somewhat frequently if it's used regularly.  

### Other Wi-Fi
#### Bread Lamp Dimmer
I wrote about this in my [first post](/blog/firstpost). 

#### Dreo TurboPoly Fan 704S
I added [this fan](https://www.dreo.com/products/turbopoly-fan-704s) to Home Assistant using the cloud-based [Dreo integration](https://github.com/JeffSteinbok/hass-dreo). The fan is a good product (energy efficient, customizable vertical/horizontal oscillation and position, and quiet) but the Home Assistant integration did fail for a while because the Dreo SSL certificate expired. Home Assistant connectivity to this fan is not critical, but I learned to not rely on cloud-based integrations if I care about reliably controlling a device through Home Assistant. 

#### Nanoleaf Multicolor Floor Lamp
This [lamp](https://us-shop.nanoleaf.me/products/multicolor-floor-lamp) is basically a light strip on a base (the installation involved stuffing a light strip into the tube) and a version of it is made by several companies ([Hue Signe](https://www.philips-hue.com/en-us/p/hue-white-and-color-ambiance-signe-gradient-floor-lamp/046677803506), [SwitchBot](https://us.switch-bot.com/products/switchbot-rgbicww-floor-lamp), [Govee](https://us.govee.com/products/govee-floor-lamp-2)). It only supports single colors through Matter, so it's recommended to use the Nanoleaf integration to set scenes and multiple colors. As of December 2025 the core integration has not been updated to support this lamp so the [custom component version](https://github.com/loebi-ch/nanoleaf) should be used instead. 

#### Nanoleaf Shapes Triangles
I got these [lights](https://us-shop.nanoleaf.me/products/shapes-triangles-smarter-kit-7pk) from FaceBook Marketplace primarily for their built-in Thread Border Router but they put out a good amount of light too and work well as part of my bedroom light setup to wake me up in the morning. I don't use the scenes or multicolor functionality on these much. 

#### Mi Desk Lamp 1S
I purchased this lamp at a very affordable price in China. It only has a few color temperature settings and does not get as warm as my other lights. I also find the lowest brightness setting a bit too bright, but overall it's a solid light. It has some connectivity issues when far away from the router but has been reliable lately. I use the [HomeKit Device](https://www.home-assistant.io/integrations/homekit_controller/) integration to control it in Home Assistant. 

#### HASS.Agent on Windows 11 Laptop
I have the [HASS.Agent](https://www.hass-agent.io/2.1/) client on my laptop to use its active status for automations (keeping the lights on and turning on the fan). The client also shows the active window, which may be useful for some automations. 

#### Blueair Blue Pure 411i Max 
I have the air purifier connected to Home Assistant using the [ha_blueair](https://github.com/dahlb/ha_blueair) integration but it runs in auto mode all the time so I don't really use the integration for anything. 

#### GE Washer & Dryer 
I use the [ha_gehome integration](https://github.com/simbaja/ha_gehome) to connect to my washer and dryer. The integration tells me when they are done but I haven't set up any automations yet. 