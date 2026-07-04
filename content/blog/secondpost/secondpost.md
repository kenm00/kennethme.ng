---
title:  My Home Assistant Devices
description: A running list of my Home Assistant devices.
date: 2025-12-26
tags: Home Assistant
---

Updated: 07/03/2026

A running list of my devices that are connected to Home Assistant. It would probably make more sense to organize by category (lighting, climate, etc.) but my brain prefers to organize them by communication protocol so I will do that here. 

## Overview
- 26 Wi-Fi devices
  - 4 ESPHome devices
  - 2 WLED devices
  - 5 Matter-over-WiFi devices
  - 1 HomeKit device
  - 14 Other Wi-Fi devices
- 25 Matter-over-Thread devices

## Devices 
### ESPHome 
#### Apollo MSR-2 mmWave Multisensor 2x
I got two of [these sensors](https://apolloautomation.com/products/msr-2) to detect presence in zones in my living room/kitchen area and master bedroom/bathroom. The mmWave detection makes it possible to measure distance from the sensor and define 3 zones, which I use to differentiate between presence in the kitchen vs. couch/desk area and turn on different lights (I have it placed in the corner of the room on the fridge). This sensor is purportedly better at detecting still objects than the [Apollo MTR-1 multi-target multisensor](https://apolloautomation.com/products/mtr-1), but it tends to clear my presence once I have been still for a few minutes. It works better when multiple people are present. I increased the timeout of my automations to 10 minutes to prevent the lights from turning off when I'm still there. 

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
This [microcontroller](https://www.adafruit.com/product/5778) has built-in pins to connect to HUB75 matrix panel (I have [this 32x32 one](https://www.adafruit.com/product/607)). I originally got it to also connect a temperature sensor and IR transmitter to also use it as a thermostat but decided to do that separately (see [ESP8285](#esp8285-with-ir-transmitter-2x) below). Instead I installed [WLED MoonModules](https://mm.kno.wled.ge/), which has support for 2D panels and is a lot easier to control and customize than something I could program using Arduino. It has some pretty cool built-in effects. I still need to make an enclosure for the panel, however.  

#### SK6812RGBW LED Strip with ESP8266
This is an old [RGBW strip](https://www.adafruit.com/product/2828) and [ESP8266 microcontroller](https://www.adafruit.com/product/2821) I had lying around that I originally had manually programmed with Arduino but reflashed with WLED. I mounted this within a window. 

### Bluetooth
#### SwitchBot Bot 
I have this attached to the bathroom ventilation fan switch to schedule it to turn on every morning to keep the air fresh. 

#### CCT FOB Lightstrip
[This lightstrip](https://a.co/d/09lAagB8) connects over bluetooth using the Elkbledom integration. It's very affordable but can be slightly unreliable when turning on. Also, it does not support setting brightness/color temperature and turning on in one command, so the separate_turn_on_commands option must be enabled in adaptive lighting for it to work. I have it mounted inside my closet where it turns on using the IKEA Myggbett contact sensor when the door is opened. 

### Matter-over-Thread
#### Nanoleaf Essentials Matter A19 E26 Smart Bulb 3x
[These](https://us-shop.nanoleaf.me/products/matter-thread-a19-smart-bulbs-3pk) were on sale for a steep discount (~$13 for 3) at Home Depot. They appear to be discontinued now in favor of a Wi-Fi version, but I prefer Thread devices. They are generally reliable over Thread but do seem to take a beat longer to respond than other lights sometimes. I have also had to reset some of the bulbs after they stopped responding. The color quality is decent but the warm whites can look a little bit off.  

#### Philips Hue Essential A19 E26 Smart Bulb 4x
I got a four-pack of [these](https://www.philips-hue.com/en-us/p/hue-white-and-color-ambiance-essential-a19-e26-smart-bulb-800-lm-88w/046677592530) since I was curious about Hue's more affordable option. I have them installed in the bathroom and they are responsive. They do not dim very low, however.  

#### IKEA Timmerflotte Temperature/Humidity Sensor 2x
Basic temperature and humidity sensors that display the readings when clicked. 

#### IKEA BILRESA Dual Smart Button 3x
Affordable smart buttons with two buttons. I use [this blueprint](https://community.home-assistant.io/t/ikea-bilresa-dual-button-remote/965815) to assign actions for single press, double press, and pressing and holding each button. These are generally reliable and responsive. 

#### IKEA BILRESA Scroll Wheel
Affordable smart button that allows you to select between three modes and has a scroll wheel that also clicks. I have not been able to get this to work responsively or reliably using a blueprint. 

#### IKEA KAJPLATS LED Bulb E26 800 lumen, smart white spectrum/clear/globe, 4"
I got this for an exposed fixture in the kitchen. The bulb looks nice and is responsive. I am not aware of other options for filament smart bulbs that use Matter-over-Thread. 

#### IKEA Myggbett Contact Sensor 4x
Compact and cheap contact sensors that I am using for a window and three doors. The one attached to the window automatically turns off climate control when opened. The ones attached to interior doors control lighting, and the one attached to the front door enables the Nuki smart lock to auto-lock as soon as the door is closed. 

#### IKEA Myggspray Motion Sensor 3x
Basic motion sensors without the ability to fine-tune detection. I use them to turn on lights in the hallway, bathroom, and bedroom where zone lighting control (which I achieve usin the MSR-2 multisensor) is not necessary. 

#### IKEA Grillplats Smart Plug 3x 
Cheap smart plugs with energy monitoring that I use for an old iOS device to control its charge level. I also use this to monitor energy usage on a mini PC. 

#### Nuki Smart Lock with Keypad 2 NFC 
This retrofit smart lock is one of the few options that uses Matter-over-Thread. My other option would have been the Level Bolt, but [that company appears to have been gutted by Assa Abloy](https://www.theverge.com/tech/957802/level-lock-layoffs-assa-abloy-kwikset-smart-lock-cloud). The Nuki is very compact and fast. So far the battery has been depleting faster than expected but I am still monitoring. The Keypad 2 NFC is one of the first to support the Aliro standard for NFC unlocking (which maybe explains its very high price). Auto-unlock (geofence and bluetooth based) and keypad unlock using Apple Home Key or fingerprint all work reliably and quickly. Instead of getting the $59 Nuki door sensor I use the IKEA Myggbett sensor and an HA automation to automatically lock as soon as the door is closed. 

### Matter-over-WiFi 
#### GE Cync Reveal Undercabinet Bar Lights 2x
This [light](https://www.gelighting.com/smart-home/fixtures/undercabinet) seems to be one of the few Matter-enabled lights designed for undercabinet use (no visible individual LEDs). I have them installed using command strips to my cabinets and the light quality is good (I usually only use the shades of white). I had some issues during setup when one wouldn't add to apple Home but a restart fixed it. They also sometimes have trouble updating their brightness and color temperature fully when Home Assistant sends a commands to update them. I'm not sure if that's an issue with the [adaptive lighting](https://www.gelighting.com/smart-home/fixtures/undercabinet) component in HA, but I have tried various changes to my adaptive lighting configuration and they still exhibit the issue intermittently. Overall I would recommend these (given the limited options for this form factor) if you don't intend on using them with adaptive lighting, but there are more options for strip lights and at least some of them likely have fewer connectivity issues.   

#### SwitchBot Candle Warmer Lamp
I had been thinking about how candles can be made smart when SwitchBot announced this [product](https://us.switch-bot.com/products/switchbot-candle-warmer-lamp?variant=49194196107497). I preordered it the same day and it works as expected with Apple Home and Home Assistant. It appears to throw the scent a bit farther than burning a candle, and puts out a good amount of light too. It uses a 50W halogen bulb which will need to be replaced somewhat frequently if it's used regularly.  

#### LIFX BR30 2x
This [bulb](https://www.lifx.com/products/lifx-color-br30-1pk) is bright and rated for outdoor use, and I have one installed on my balcony. They are reliable and responsive. An upgrade to Thread connectivity is [expected later in 2026](https://www.lifx.com/blogs/learn/ces-2026), but we will see. Do not buy a product based on promises of future functionality. Worth noting that the Matter and non-Matter versions were being sold interchangeably so I purchased in-person to check the packaging for the Matter logo before buying. 

### Other Wi-Fi
#### Bread Lamp Dimmer
I wrote about this in my [first post](/blog/firstpost). 

#### Dreo TurboPoly Fan 704S
I added [this fan](https://www.dreo.com/products/turbopoly-fan-704s) to Home Assistant using the cloud-based [Dreo integration](https://github.com/JeffSteinbok/hass-dreo). The fan is a good product (energy efficient, customizable vertical/horizontal oscillation and position, and quiet) but the Home Assistant integration did fail for a while because the Dreo SSL certificate expired. Home Assistant connectivity to this fan is not critical, but I learned to not rely on cloud-based integrations for reliability. 

#### Nanoleaf Multicolor Floor Lamp
This [lamp](https://us-shop.nanoleaf.me/products/multicolor-floor-lamp) is basically a light strip on a base (the installation involved stuffing a light strip into the tube) and a version of it is made by several companies ([Hue Signe](https://www.philips-hue.com/en-us/p/hue-white-and-color-ambiance-signe-gradient-floor-lamp/046677803506), [SwitchBot](https://us.switch-bot.com/products/switchbot-rgbicww-floor-lamp), [Govee](https://us.govee.com/products/govee-floor-lamp-2)). It only supports single colors through Matter, so it's recommended to use the Nanoleaf integration to set scenes and multiple colors. As of December 2025 the core integration has not been updated to support this lamp so the [custom component version](https://github.com/loebi-ch/nanoleaf) should be used instead. 

#### Nanoleaf Shapes Triangles
I got these [lights](https://us-shop.nanoleaf.me/products/shapes-triangles-smarter-kit-7pk) from FaceBook Marketplace primarily for their built-in Thread Border Router but they put out a good amount of light too and work well as part of my bedroom light setup to wake me up in the morning. I don't use the scenes or multicolor functionality on these much. 

#### Mi Desk Lamp 1S
I purchased this lamp at a very affordable price in China. It only has a few color temperature settings and does not get as warm as my other lights. I also find the lowest brightness setting a bit too bright, but overall it's a solid light. It has some connectivity issues when far away from the router but has been reliable lately. I use the [HomeKit Device](https://www.home-assistant.io/integrations/homekit_controller/) integration to control it in Home Assistant. 

#### LIFX Outdoor Multicolor String Lights 
These [lights](https://www.lifx.com/products/lifx-string-lights) look good but are pretty expensive for the length and number of lights. I hang them on my balcony. They are Matter-compatible but I control them with the LIFX integration to control effects and color palette. 

#### Wemo mini 2x 
These have been killed by Wemo (very bad) but I used [Pywemogui](https://github.com/ThatStella7922/PyWeMoGUI) to set them up on my network and connect to them using the local wemo integration. They still work well and demonstrate the importance of local control over smart home devices. 

#### HASS.Agent on Windows 11 Laptop
I have the [HASS.Agent](https://www.hass-agent.io/2.1/) client on my laptop to use its active status for automations (keeping the lights on and turning on the fan). The client also shows the active window, which may be useful for some automations. 

#### LG C2 OLED 42"
I got this secondhand and use the LG WebOS TV integration to see it in Home Assistant. I can see the status of the TV and turn it on using Wake-on-LAN but have not been able to turn it off through HA. 

#### TP-Link Tapo C120 Camera 
I use this to monitor my cat. The [Tapo Cameras Control](https://github.com/jurajnyiri/homeassistant-tapo-control) custom integration works well for controlling the camera. The camera is automatically set to privacy mode when I am home, but I also have tape on the camera just in case. 

#### Blueair Blue Pure 411i Max 
I have the air purifier connected to Home Assistant using the [ha_blueair](https://github.com/dahlb/ha_blueair) integration but it runs in auto mode all the time so I don't really use the integration for anything. 

#### GE Washer & Dryer 
I use the [ha_gehome integration](https://github.com/simbaja/ha_gehome) to connect to my washer and dryer. HA notifies me when they are almost done. 

#### Pawsync Automatic Feeder
An automatic feeder that uses a scale for more accurate feeding amounts. The [HA integration](https://github.com/jasonmeisel/pawsync-hass) (cloud-based) is fairly basic but tells me the amount of food remaining which is useful. 

#### Catlink Open-X (C08) Automatic Litter Box
An affordable open-design automatic litter box. The [custom component](https://github.com/hasscc/catlink) (cloud-based) exposes a lot of information to HA but I haven't used it for automations yet. 
