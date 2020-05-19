---
title: DETA Grid Connect Smart LED Downlight
date-published: 2020-05-18
type: light

standard: au
---

1. TOC
{:toc}

## General Notes

The DETA [Smart LED Downlight 7W DET900HA](https://www.bunnings.com.au/deta-7w-smart-led-downlight-with-grid-connect_p0102572) and [Smart LED Downlight 10W DET902HA](https://www.bunnings.com.au/deta-10w-smart-led-downlight-with-grid-connect_p0102573) are made by Arlec as part of the [Grid Connect ecosystem](https://grid-connect.com.au/), and are sold at Bunnings in Australia and New Zealand.  They can be flashed without disassembly or soldering [using tuya-convert](#tuya-convert).

## GPIO Pinout
Source: [https://templates.blakadder.com/deta_DET902HA.html](https://templates.blakadder.com/deta_DET902HA.html)

| Pin     | Function                           |
|---------|------------------------------------|
| GPIO5   | Red                                |
| GPIO4   | Green                              |
| GPIO13  | Blue                               |
| GPIO14  | Warm White                         |
| GPIO12  | Cold White                               |

## Getting it up and running

### Tuya Convert

Flash wirelessly with Tuya-Convert
- Put the airing mode by repeating the power on/off cycles 5 times, with 1 second in each.
- The downlight blinks rapidly to confirm that it has entered pairing mode.

### RGBWW
The default light platform RGBWW have sliders that aren't intuitive to use.  Recommend substituting the code by "displaced" for a more intuitive experience.
([https://community.home-assistant.io/t/cw-ww-and-colour-temperature-slider-support-in-custom-light-output/148098/11](https://community.home-assistant.io/t/cw-ww-and-colour-temperature-slider-support-in-custom-light-output/148098/11))

### Transformer
 - The wireless control module is located in the transformer.  
 - The terminals on the mains power side of the transformer is rather small.


## Basic Configuration

```yaml
# device declaration: Deta DET900HA 7W 660lm RGB+CCT downlight
# buying from: https://www.bunnings.com.au/deta-7w-smart-led-downlight-with-grid-connect_p0102572
#
# Note1: This will work for Deta DET902HA 10W 940lm RGB+CCT Light
#        https://www.bunnings.com.au/deta-10w-smart-led-downlight-with-grid-connect_p0102573

# variables
substitutions:
  friendly_name: 'Fancy Device'
  device_name: 'fancy_device'
  reboot_timeout: 1h
  update_interval: 1min
  #static_ip: 10.10.10.88

# core configuration
esphome:
  name: ${device_name}
  platform: ESP8266
  board: esp01_1m
  esp8266_restore_from_flash: true

# WiFi + network settings
wifi:
  ssid: 'Name of you homes WiFi'
  password: 'your supersecret wifi password'
  fast_connect: true
  reboot_timeout: ${reboot_timeout}
  #manual_ip:
  #  static_ip: ${static_ip}
  #  gateway: 10.10.10.1
  #  subnet: 255.255.255.0
  #  dns1: 10.10.10.1
  #  dns2: 1.1.1.1
  #ap:
  #  ssid: '${name} AP (192.168.4.1)'
  #  password: '1234abcd'         #  wifi password when in access point mode. Leave '' for no password.

# captive portal for access point mode
captive_portal:

# Logging
logger:
  # level: DEBUG
  # Disable logging to serial
  # baud_rate: 0

# enabling home assistant legacy api
api:
  password: 'your secret api password'

# enabling over the air updates
ota:
  password: 'your secret ota password'

# synchronizing time with home assistant
time:
  - platform: homeassistant
    id: homeassistant_time

# Defining the output pins
output:
  - platform: esp8266_pwm
    id: output_red
    pin: GPIO5 #PWM1
  - platform: esp8266_pwm
    id: output_green
    pin: GPIO4 #PWM2
  - platform: esp8266_pwm
    id: output_blue
    pin: GPIO13 #PWM3
  - platform: esp8266_pwm
    id: output_warm_white
    pin: GPIO14 #PWM4
  - platform: esp8266_pwm
    id: output_cold_white
    pin: GPIO12 #PWM5

# here go the light definitions, effects and restore mode
light:
  - platform: rgbww
    name: '${friendly_name}'
    id: '${device_name}'
    red: output_red
    green: output_green
    blue: output_blue
    warm_white: output_warm_white
    cold_white: output_cold_white
    warm_white_color_temperature: 2800 K
    cold_white_color_temperature: 6200 K

    effects:
      - random:
      - random:
          name: 'random slow'
          update_interval: 30s
          transition_length: 7.5s

    # Attempt to restore state and default to ON if the physical switch is actuated.
    restore_mode: RESTORE_DEFAULT_ON

```
