---
layout: page
title: "Mill heater"
description: "Instructions on how to integrate Mill heater into Home Assistant."
date: 2018-10-10 15:00 +0200
sidebar: true
comments: false
sharing: true
footer: true
logo: mill.png
ha_category: Climate
ha_release: 0.81
ha_iot_class: "Cloud Polling"
---

Integrates Mill heater into Home Assistant.

## {% linkable_title Configuration %}

To enable this platform, add the following lines to your `configuration.yaml` file:

```yaml
climate:
  - platform: mill
    username: YOUR_EMAIL_ADDRESS
    password: YOUR_PASSWORD
```

{% configuration %}
username:
  description: Your registered Mill email.
  required: true
  type: string
password:
  description: Your Mill password.
  required: true
  type: string 
{% endconfiguration %}

## {% linkable_title Component services %}

This platform supports a service to set the temperature for the room connected to heater in the Mill app:

`climate.mill_set_room_temperature`


| Service data attribute | Optional | Description |
| ---------------------- | -------- | ----------- |
| `room_name` | no | String with room name.
| `away_temp` | yes | Integer with temperature
| `comfort_temp` | yes | Integer with temperature
| `sleep_temp` | yes | Integer with temperature
