---
layout: page
title: "Generic Thermostat"
description: "Turn Home Assistant into a thermostat"
date: 2015-03-23 19:59
sidebar: true
comments: false
sharing: true
footer: true
logo: home-assistant.png
ha_category: Climate
ha_release: pre 0.7
ha_iot_class: "Local Polling"
---

The `generic_thermostat` climate platform is a thermostat implemented in Home Assistant. It uses a sensor and a switch connected to a heater or air conditioning under the hood. When in heater mode, if the measured temperature is cooler than the target temperature, the heater will be turned on and turned off when the required temperature is reached. When in air conditioning mode, if the measured temperature is hotter than the target temperature, the air conditioning will be turned on and turned off when required temperature is reached. One Generic Thermostat entity can only control one switch. If you need to activate two switches, one for a heater and one for an air conditioner, you will need two Generic Thermostat entities.

```yaml
# Example configuration.yaml entry
climate:
  - platform: generic_thermostat
    name: Study
    heater: switch.study_heater
    target_sensor: sensor.study_temperature
```

{% configuration %}
name:
  description: Name of thermostat.
  required: true
  default: Generic Thermostat
  type: string
heater:
  description: "`entity_id` for heater switch, must be a toggle device. Becomes air conditioning switch when `ac_mode` is set to `true`."
  required: true
  type: string
target_sensor:
  description: "`entity_id` for a temperature sensor, target_sensor.state must be temperature."
  required: true
  type: string
min_temp:
  description: Set minimum set point available.
  required: false
  default: 7
  type: float
max_temp:
  description: Set maximum set point available.
  required: false
  default: 35
  type: float
target_temp:
  description: Set initial target temperature. Failure to set this variable will result in target temperature being set to null on startup. As of version 0.59, it will retain the target temperature set before restart if available.
  required: false
  type: float
ac_mode:
  description: Set the switch specified in the *heater* option to be treated as a cooling device instead of a heating device.
  required: false
  type: boolean
min_cycle_duration:
  description: Set a minimum amount of time that the switch specified in the *heater* option must be in its current state prior to being switched either off or on.
  required: false
  type: [time, integer]
cold_tolerance:
  description: Set a minimum amount of difference between the temperature read by the sensor specified in the *target_sensor* option and the target temperature that must change prior to being switched on. For example, if the target temperature is 25 and the tolerance is 0.5 the heater will start when the sensor equals or goes below 24.5.
  required: false
  default: 0.3
  type: float
hot_tolerance:
  description: Set a minimum amount of difference between the temperature read by the sensor specified in the *target_sensor* option and the target temperature that must change prior to being switched off. For example, if the target temperature is 25 and the tolerance is 0.5 the heater will stop when the sensor equals or goes above 25.5.
  required: false
  default: 0.3
  type: float
keep_alive:
  description: Set a keep-alive interval. If set, the switch specified in the *heater* option will be triggered every time the interval elapses. Use with heaters and A/C units that shut off if they don't receive a signal from their remote for a while. Use also with switches that might lose state. The keep-alive call is done with the current valid climate component state (either on or off).
  required: false
  type: [time, integer]
initial_operation_mode:
  description: Set the initial operation mode. Valid values are `off` or `auto`. Value has to be double quoted. If this parameter is not set, it is preferable to set a *keep_alive* value. This is helpful to align any discrepancies between *generic_thermostat* and *heater* state.
  required: false
  type: string
away_temp:
  description: Set the temperature used by "away_mode". If this is not specified, away_mode feature will not get activated.
  required: false
  type: float
precision:
  description: "The desired precision for this device. Can be used to match your actual thermostat's precision. Supported values are `0.1`, `0.5` and `1.0`."
  required: false
  type: float
  default: "`0.5` for Celsius and `1.0` for Fahrenheit."
{% endconfiguration %}

A full configuration example looks like the one below. `min_cycle_duration` and `keep_alive` must contain at least one of the following entries: `days:`, `hours:`, `minutes:`, `seconds:` or `milliseconds:`.

Currently the `generic_thermostat` climate platform supports 'heat', 'cool' and 'off' operation modes. You can force your `generic_thermostat` to avoid starting by setting Operation to 'off'.

Please note that changing Away Mode you will force a target temperature change as well that will get restored once the Away Mode is turned off.

```yaml
# Full example configuration.yaml entry
climate:
  - platform: generic_thermostat
    name: Study
    heater: switch.study_heater
    target_sensor: sensor.study_temperature
    min_temp: 15
    max_temp: 21
    ac_mode: False
    target_temp: 17
    cold_tolerance: 0.3
    hot_tolerance: 0
    min_cycle_duration:
      seconds: 5
    keep_alive:
      minutes: 3
    initial_operation_mode: "off"
    away_temp: 16
    precision: 0.1
```
