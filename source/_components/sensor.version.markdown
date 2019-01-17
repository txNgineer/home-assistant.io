---
layout: page
title: "Version Sensor"
description: "Instructions on how to integrate a version sensor into Home Assistant."
date: 2017-08-10 10:30
sidebar: true
comments: false
sharing: true
footer: true
ha_category: Utility
ha_iot_class: "Local Pushing"
logo: home-assistant.png
ha_release: 0.52
ha_qa_scale: internal
---

The `version` sensor platform that can display the current Home Assistant versions.

## {% linkable_title Configuration %}

To enable this sensor, add the following lines to your `configuration.yaml`:

```yaml
# Example configuration.yaml entry
sensor:
  - platform: version
```

{% configuration %}
name:
  description: Name to use in the frontend.
  required: false
  type: string
  default: "`Current Version` in case of `source: local`, `Latest Version` otherwise"
beta:
  description: Flag to indicate that it will check for beta versions, only supported for the sources `pypi`, `hassio` and `docker`.
  required: false
  type: boolean
  default: false
image:
  description: The image you want to check against, this is only supported for `hassio`, see full list under.
  required: false
  type: string
  default: default
source:
  description: The source you want to check against, possible values are `local`, `pypi`, `hassio` and `docker`.
  required: false
  type: string
  default: local
{% endconfiguration %}

### {% linkable_title Supported images for Hassio %}

`default`, `qemux86`, `qemux86-64`, `qemuarm`, `qemuarm-64`, `intel-nuc`, `raspberrypi`, `raspberrypi2`, `raspberrypi3`, `raspberrypi3-64`, `tinker`, `odroid-c2`, `odroid-xu`

## {% linkable_title Alternatives for showing local version %}

This sensor is an alternative to the existing solutions to achieve the same
result through various platforms.
Remember that you can easily get the installed version on the command line.

```bash
$ hass --version
```

Or go to the <img src='/images/screenshots/developer-tool-about-icon.png' alt='service developer tool icon' class="no-shadow" height="38" /> **Info** section of the **Developer Tools**.

A [`command_line`](/components/sensor.command_line/) with
[`hass`](/docs/tools/hass/) to display your current version.

```yaml
sensor:
  - platform: command_line
    name: Version
    command: "/home/homeassistant/bin/hass --version"
```

It's also possible to read a file called `.HA_VERSION` which is located in your
Home Assistant [configuration](/docs/configuration/) folder.

```yaml
sensor:
  - platform: command_line
    name: Version
    command: "cat /home/homeassistant/.homeassistant/.HA_VERSION"
```

You might think that a [`rest` sensor](/components/sensor.rest/) could work,
too,
but it will not as Home Assistant is not ready when the sensor gets initialized.

{% raw %}
```yaml
sensor:
  - platform: rest
    resource: http://IP_ADDRESS:8123/api/config
    name: Current Version
    value_template: '{{ value_json.version }}'
```
{% endraw %}
