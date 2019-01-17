---
layout: page
title: PocketCasts
description: "Instructions on how to set up PocketCasts sensors within Home Assistant."
date: 2017-02-14 08:00
sidebar: true
comments: false
sharing: true
footer: true
logo: pocketcasts.png
ha_category: Multimedia
ha_release: 0.39
ha_iot_class: "Cloud Polling"
---

The `pocketcasts` sensor platform let one monitor the podcasts at [Pocket Casts](https://play.pocketcasts.com/). 

## {% linkable_title Configuration %}

To enable this sensor, add the following lines to your `configuration.yaml`:

```yaml
# Example configuration.yaml entry
sensor:
  - platform: pocketcasts
    username: YOUR_USERNAME
    password: YOUR_PASSWORD
```

{% configuration %}
username:
  description: The username to access the PocketCasts service.
  required: true
  type: string
password:
  description: The password for the given username.
  required: true
  type: string
{% endconfiguration %}
