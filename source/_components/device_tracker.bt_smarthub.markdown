---
layout: page
title: "BT Smart Hub"
description: "Instructions on how to integrate BT Smart Hub router into Home Assistant."
date: 2018-10-05 10:00
sidebar: true
comments: false
sharing: true
footer: true
logo: bt.png
ha_category: Presence Detection
ha_release: "0.82"
ha_iot_class: "Local Polling"
---

This platform offers presence detection by looking at connected devices to a [BT Smart Hub](https://en.wikipedia.org/wiki/BT_Smart_Hub) based router.
This router is sometimes referred to as the BT Home Hub 6.

## {% linkable_title Configuration %}

To use a BT Smart Hub router in your installation, add the following to your `configuration.yaml` file:

```yaml
# Example configuration.yaml entry
device_tracker:
  - platform: bt_smarthub
```

{% configuration %}
host:
  description: The IP address of your router 
  default: 192.168.1.254
  required: false
  type: string
{% endconfiguration %}

See the [device tracker component page](/components/device_tracker/) for instructions on how to configure the people to be tracked.
