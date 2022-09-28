---
title: "Home Automation"
date: 2022-09-27T21:03:11-05:00
draft: true

slug: home-automation # Will take the filename as the slug.

# Lists
keywords: homelab madscience homeassistant mycroft
tags: madscience homelab homeassistant mycroft
categories: home automation homelab
---

# Overview

My goal is to keep my home automation as local as possible. I have a few rules:

1. Everything has a manual override. If we lose internet, everything should still function as much as possible. This means occasionally purchasing extra hardware that seems silly - until all my stuff still works.
1. Nothing is network-enabled that can grant access to my home or burn it down.
1. Ideally, everything can be managed via Home Assistant.
1. Everything should be local wherever possible, i.e. no internet access required for functionality.
1. Avoid shipping any data or network access to any external company.
1. Z-Wave over Zigbee.

As always, rules are made to be broken, and this is aspirational where viable alternatives simply don't exist.

Everything operates via Home Assistant but until I can get my smart assistants set up with [Mycroft](https://mycroft.ai), Alexa rules my home. Built-in Alexa is a dealbreaker for me on smart hardware.

## Smart Power Strips

[TP-Link Kasa HS300](https://www.amazon.com/gp/product/B07G95FFN3/)
Used [this script](https://github.com/p-doyle/Python-KasaSmartPowerStrip) to set up local internet, connecting to the strip's local wifi and executing:

```python
power_strip = SmartPowerStrip("192.168.0.1")
power_strip.set_wifi_credentials("MYSSID", "MYPASSWORD", "3")
```

Blocked tplinkcloud.com via OpenDNS to prevent the power strip from reporting home or receiving updates. Eventually I'd like to flash the firmware altogether but that's going to be more work since they aren't using standard chips.

## Smart Plugs

- [Sonoff S31 plugs](https://www.amazon.com/gp/product/B08X2944W7/) flashed with [Tasmota](https://tasmota.github.io/docs/).

Setup:

- [Ideal article for hardware portion](http://lukeknipe.com/s31-tasmota/) but the web flashing utility for Tasmota is insecure in most browsers.
- [How I actually flashed them](https://www.adventurousway.com/blog/sonoff-s31)
- [Great explanation of using a graphical utility to flash it, plus setting up Home Assistant and MQTT if you don't already have it set up](https://medium.com/@jordanrounds/sonoff-basic-r2-tasmota-aa6f9d4e033f)

## Smart Doorbell

- [Amcrest 1080P Video Doorbell Camera Pro](https://www.amazon.com/gp/product/B07ZJS3L5Y/)
  Didn't activate the free cloud storage. Set up Surveillance Center on my Synology NAS and access it directly there via Home Assistant.

Eventually I want to block the doorbell's outbound access and set up better alerting, but I need to look at what's exposed locally before I make any decisions.

## HVAC

- [Ecobee3 Lite Smart Thermostat, 2nd Gen](https://www.amazon.com/gp/product/B06W56TBLN/)
- [Ecobee SmartSensors](https://www.amazon.com/gp/product/B07NQVWRR3/)

## Downstairs Lights

- [Inovelli Red Z-Wave Switches](https://www.amazon.com/gp/product/B07T26MVYC/)

## Home Entertainment

- [Logitech Harmony Hub](https://www.amazon.com/Logitech-Harmony-Control-Entertainment-Devices/dp/B00N3RFC4Q/)
- [Plex](https://plex.tv) running on the Synology, connected to my local media files.

## Cleaning

- [iRobot Roomba 675](https://www.amazon.com/iRobot-Vacuum-Wi-Fi-Connectivity-Carpets-Self-Charging/dp/B089TNN8XQ/)

## Core Services

- [Synology DS920+](https://www.amazon.com/Synology-DiskStation-DS920-Diskless-4-bay/dp/B087Z34F3R/)
- Home Assistant, running Home Assistant OS on a Raspberry Pi 3
- [Zooz 700 Series Z-Wave Plus S2 USB Stick](https://www.amazon.com/gp/product/B07GNZ56BK/)
- [Google Home Mini or Nest Mini](https://www.target.com/p/google-nest-mini-2nd-generation-chalk/-/A-76500134) in each room, with the microphone switched off and no reporting services, using Home Assistant to set up AirPlay for each of them. This allows me to use them as HA `media_player` sources and send messages throughout the house for various automations. Inexpensive, reasonable quality for TTS voice messages, and the only commercial smart assistant I could find with a real hardware microphone switch.
- Node-RED for automations in Home Assistant
- [Google Wifi - AC1200 - Mesh WiFi System](https://www.amazon.com/gp/product/B08GG9CMLR/)
- A Raspberry Pi 3B+ print server running Ubuntu 22.04 lite. Since I rarely use Windows at home but we almost exclusively have Apple devices, the printer is served via CUPS. Native AirPrint support and now I can wirelessly print from every device I use regularly at home, including our phones and iPads.

**Note**: Yes, I realize using Google for my mesh wifi is in violation of a lot of principles in my home automation. However, it seems to be the least invasive of the mesh wifi options available. Nobody is offering mesh wifi at any kind of reasonable rate without shipping data off to who-knows-where. The devil you know is better than the devil you don't.

## Triggers

- [NFC215 Coins](https://www.amazon.com/gp/product/B08DXSQVYH/) for triggering automations via my phone.

## Upcoming

- [Shelly relays](https://www.amazon.com/gp/product/B07XRY1K7V) to turn dumb light switches into smart ones (linked example is for switches lacking a neutral wire, which unfortunately several of my rooms lack)
- 3D Printer
- Homemade motion sensors
- Raspberry Pi clusters to perform tasks and process data, using the Synology as long-term storage
- Software-Defined Radio set up on one of the Pis
- Backups for the Synology (mostly media and files), probably up to Google Drive due to low costs
- Local Git setup with CI/CD (GitLab or similar)
- Hashistack
- Tailscale
- Machine Learning algorithm training on the gaming laptop at idle
