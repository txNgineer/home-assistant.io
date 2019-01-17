---
layout: page
title: "Volvo On Call"
description: "Instructions for how to integrate Volvo On Call into Home Assistant."
date: 2016-10-02 17:00
sidebar: true
comments: false
sharing: true
footer: true
logo: volvo.png
ha_category: Car
ha_release: 0.39
ha_iot_class: "Cloud Polling"
redirect_from:
 - /components/binary_sensor.volvooncall/
 - /components/lock.volvooncall/
 - /components/sensor.volvooncall/
 - /components/switch.volvooncall/
 - /components/device_tracker.volvooncall/
---

The `volvooncall` component offers integration with the [Volvo On Call](http://www.volvocars.com/intl/own/connectivity/volvo-on-call) cloud service and offers presence detection as well as sensors such as odometer and fuel level.

## {% linkable_title Configuration %}

To use Volvo On Call in your installation, add the following to your `configuration.yaml` file:

```yaml
# Example configuration.yaml entry
volvooncall:
  username: YOUR_USERNAME
  password: YOUR_PASSWORD
```

Users registered with Volvo in North America or China will need to specify a region:

```yaml
# North America
volvooncall:
  username: YOUR_USERNAME
  password: YOUR_PASSWORD
  region: na
```

or

```yaml
# China
volvooncall:
  username: YOUR_USERNAME
  password: YOUR_PASSWORD
  region: cn
```

{% configuration %}
username:
  description: The username associated with your Volvo On Call account.
  required: true
  type: string
password:
  description: The password for your given Volvo On Call account.
  required: true
  type: string
region:
  description: The region where the Volvo is registered. Needs to be set for users in North America or China.
  required: false
  type: string
service_url:
  description: The service URL to use for Volvo On Call. Normally not necessary to specify.
  required: false
  type: string
mutable:
  description: If set to true, include components that can make changes to the vehicle (unlock, start engine, start heater etc).
  required: false
  default: true
  type: boolean
name:
  description: Make it possible to provide a name for the vehicles.
  required: false
  type: string
resources:
  description: A list of resources to display (defaults to all available).
  required: false
  type: list
scandinavian_miles:
  description: If set to true, Scandinavian miles ("mil") are used for distances and fuel range.
  required: false
  type: boolean
  default: false
{% endconfiguration %}

### {% linkable_title Available Resources %}

The list of currently available resources:

- `position`
- `lock`
- `heater`
- `odometer`
- `trip_meter1`
- `trip_meter2`
- `fuel_amount`
- `fuel_amount_level`
- `average_fuel_consumption`
- `distance_to_empty`
- `washer_fluid_level`
- `brake_fluid`
- `service_warning_status`
- `bulb_failures`
- `battery_range`
- `battery_level`
- `time_to_fully_charged`
- `battery_charge_status`
- `engine_start`
- `last_trip`
- `is_engine_running`
- `doors.hood_open`
- `doors.front_left_door_open`
- `doors.front_right_door_open`
- `doors.rear_left_door_open`
- `doors.rear_right_door_open`
- `windows.front_left_window_open`
- `windows.front_right_window_open`
- `windows.rear_left_window_open`
- `windows.rear_right_window_open`
- `tyre_pressure.front_left_tyre_pressure`
- `tyre_pressure.front_right_tyre_pressure`
- `tyre_pressure.rear_left_tyre_pressure`
- `tyre_pressure.rear_right_tyre_pressure`
- `any_door_open`
- `any_window_open`

## {% linkable_title Advanced Examples %}

A more advanced example for setting the vehicle name and selecting what resources to display:

```yaml
# Example configuration.yaml entry
volvooncall:
  username: YOUR_USERNAME
  password: YOUR_PASSWORD
  name:
    abc123: 'Batmobile'
  resources:
    - odometer
    - lock
    - heater
```
