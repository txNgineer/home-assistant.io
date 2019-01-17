---
layout: page
title: "Setup basic information"
description: "Setting up the basic info of Home Assistant."
date: 2015-03-23 12:50
sidebar: true
comments: false
sharing: true
footer: true
redirect_from: /getting-started/basic/
---

By default, Home Assistant will try to detect your location from IP address geolocation. Home Assistant will automatically select a temperature unit and time zone based on this location. You can overwrite this by adding the following information to your `configuration.yaml`:

```yaml
homeassistant:
  latitude: 32.87336
  longitude: 117.22743
  elevation: 430
  unit_system: metric
  time_zone: America/Los_Angeles
  name: Home
  whitelist_external_dirs:
    - /usr/var/dumping-ground
    - /tmp
```

{% configuration %}
latitude:
  description: Latitude of your location required to calculate the time the sun rises and sets.
  required: false
  type: float
longitude:
  description: Longitude of your location required to calculate the time the sun rises and sets.
  required: false
  type: float
elevation:
  description: Altitude above sea level in meters. Impacts weather/sunrise data.
  required: false
  type: integer
unit_system:
  description: "`metric` for Metric, `imperial` for Imperial."
  required: false
  type: string
time_zone:
  description: "Pick your time zone from the column **TZ** of [Wikipedia's list of tz database time zones](http://en.wikipedia.org/wiki/List_of_tz_database_time_zones)"
  required: false
  type: string
name:
  description: Name of the location where Home Assistant is running.
  required: false
  type: string
customize:
  description: "[Customize](/docs/configuration/customizing-devices/) entities."
  required: false
  type: string
customize_domain:
  description: "[Customize](/docs/configuration/customizing-devices/) all entities in a domain."
  required: false
  type: string
customize_glob:
  description: "[Customize](/docs/configuration/customizing-devices/) entities matching a pattern."
  required: false
  type: string
whitelist_external_dirs:
  description: List of folders that can be used as sources for sending files.
  required: false
  type: list
{% endconfiguration %}
