---
layout: page
title: "W800rf32 Binary Sensor"
description: "Instructions on how to integrate W800rf32 binary sensors into Home Assistant."
date: 2018-10-16 12:45
sidebar: true
comments: false
sharing: true
footer: true
logo: w800rf32.png
ha_category: Binary Sensor
ha_release: 0.83
ha_iot_class: "Local Push"
---

The `w800rf32` platform supports X10 RF binary sensors such as Palm Pad 
remotes, key chain remotes, Hawkeye motion detectors, and many, many other X10 RF devices.
Some that have specifically been used with this are the KR19A keychain, MS16A motion detector
and the RSS18 four button wall mount keypad.

# Setting up your devices

Once you have set up your [w800rf32 hub](/components/w800rf32/), add the 
binary sensors to your `configuration.yaml`:

```yaml
# Example configuration.yaml entry
binary_sensor:
  - platform: w800rf32
    devices:
      a1:
        name: motion_hall
      a2:
        name: motion_kitchen
```

{% configuration %}
devices:
  description: A list of devices.
  required: true
  type: map
  keys:
    name:
      description: Override the name to use in the frontend.
      required: false
      type: string
    device_class:
      description: "The [type or class of the sensor](/components/binary_sensor/) to set the icon in the frontend."
      required: false
      type: device_class
    off_delay:
      description: For sensors that only sends 'On' state updates, this variable sets a delay after which the sensor state will be updated back to 'Off'.
      required: false
      type: integer
{% endconfiguration %}


Binary sensors have only two states - "on" and "off". Many door or window
opening sensors will send a signal each time the door/window is open or closed.
However, depending on their hardware or on their purpose,
some sensors are only able to signal their "on" state:

- Most motion sensors send a signal each time they detect motion. They stay "on" for a few seconds and go back to sleep, ready to signal other motion events. Usually, they do not send a signal when they go back to sleep.

For those devices, use the *off_delay* parameter.
It defines a delay after which a device will go back to an "Off" state.
That "Off" state will be fired internally by Home Assistant, just as if
the device fired it by itself. If a motion sensor can only send signals
once every 5 seconds, sets the *off_delay* parameter to *seconds: 5*.
