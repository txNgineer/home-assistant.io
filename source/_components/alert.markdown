---
layout: page
title: "Alert"
description: "Instructions on how to setup automatic alerts within Home Assistant."
date: 2017-01-15 20:00
sidebar: true
comments: false
sharing: true
footer: true
logo: home-assistant.png
ha_category: Automation
ha_release: 0.38
ha_qa_scale: internal
---

The `alert` component is designed to notify you when problematic issues arise.
For example, if the garage door is left open, the `alert` component can be used
remind you of this by sending you repeating notifications at customizable
intervals. This is also used for low battery sensors,
water leak sensors, or any condition that may need your attention.

Alerts will add an entity to the front end only when they are firing.
This entity allows you to silence an alert until it is resolved.

<p class='note warning'>
When using the `alert` component, it is important that the time zone used for Home Assistant and the underlying operating system match.
Failing to do so may result in multiple alerts being sent at the same time (such as when Home Assistant is set to the `America/Detroit` time zone but the operating system uses `UTC`).
</P>

### {% linkable_title Basic Example %}

The `alert` component makes use of any of the `notifications` components. To
setup the `alert` component, first, you must setup a `notification` component.
Then, add the following to your configuration file:

```yaml
# Example configuration.yaml entry
alert:
  garage_door:
    name: Garage is open
    done_message: Garage is closed
    entity_id: input_boolean.garage_door
    state: 'on'
    repeat: 30
    can_acknowledge: true
    skip_first: true
    notifiers:
      - ryans_phone
      - kristens_phone
```

{% configuration %}
name:
  description: The friendly name of the alert.
  required: true
  type: string
entity_id:
  description: The ID of the entity to watch.
  required: true
  type: string
state:
  description: The problem condition for the entity.
  required: false
  type: string
  default: on
repeat:
  description: >
    Number of minutes before the notification should be repeated.
    Can be either a number or a list of numbers.
  required: true
  type: [int, list]
can_acknowledge:
  description: Allows the alert to be unacknowledgeable.
  required: false
  type: boolean
  default: true
skip_first:
  description: >
    Controls whether the notification should be
    sent immediately or after the first delay.
  required: false
  type: boolean
  default: false
message:
  description: >
    A message to be sent after an alert transitions from `off` to `on`
    with [template][template] support.
  required: false
  type: template
done_message:
  description: >
    A message sent after an alert transitions from `on` to `off` with 
    [template][template] support. Is only sent if an alert notification 
    was sent for transitioning from `off` to `on`.
  required: false
  type: template
notifiers:
  description: "List of `notification` components to use for alerts."
  required: true
  type: list
{% endconfiguration %}

In this example, the garage door status (`input_boolean.garage_door`) is watched
and this alert will be triggered when its status is equal to `on`.
This indicates that the door has been opened. Because the `skip_first` option
was set to `true`, the first notification will not be delivered immediately.
However, every 30 minutes, a notification will be delivered until either
`input_boolean.garage_door` no longer has a state of `on` or until the alert is
acknowledged using the Home Assistant frontend.

For notifiers that require other parameters (such as `twilio_sms` which requires
you specify a `target` parameter when sending the notification), you can use the
`group` notification to wrap them for an alert.
Simply create a `group` notification type with a single notification member
(such as `twilio_sms`) specifying the required parameters other than `message`
provided by the `alert` component:

```yaml
- platform: group
  name: john_phone_sms
  services:
    - service: twilio_sms
      data:
        target: !secret john_phone
```

```yaml
alert:
  freshwater_temp_alert:
    name: "Warning: I have detected a problem with the freshwater tank temperature"
    entity_id: binary_sensor.freshwater_temperature_status
    state: 'on'
    repeat: 5
    can_acknowledge: true
    skip_first: false
    notifiers:
      - john_phone_sms
```

### {% linkable_title Complex Alert Criteria %}

By design, the `alert` component only handles very simple criteria for firing.
That is, it only checks if a single entity's state is equal to a value. At some
point, it may be desirable to have an alert with a more complex criteria.
Possibly, when a battery percentage falls below a threshold. Maybe you want to
disable the alert on certain days. Maybe the alert firing should depend on more
than one input. For all of these situations, it is best to use the alert in
conjunction with a `Template Binary Sensor`. The following example does that.

{% raw %}
```yaml
binary_sensor:
  - platform: template
    sensors:
      motion_battery_low:
        value_template: '{{ states.sensor.motion.attributes.battery < 15 }}'
        friendly_name: 'Motion battery is low'

alert:
  motion_battery:
    name: Motion Battery is Low
    entity_id: binary_sensor.motion_battery_low
    repeat: 30
    notifiers:
      - ryans_phone
      - kristens_phone
```
{% endraw %}

This example will begin firing as soon as the entity `sensor.motion`'s `battery`
attribute falls below 15. It will continue to fire until the battery attribute
raises above 15 or the alert is acknowledged on the frontend.

### {% linkable_title Dynamic Notification Delay Times %}

It may be desirable to have the delays between alert notifications dynamically
change as the alert continues to fire. This can be done by setting the `repeat`
configuration key to a list of numbers rather than a single number.
Altering the first example would look like the following.

```yaml
# Example configuration.yaml entry
alert:
  garage_door:
    name: Garage is open
    entity_id: input_boolean.garage_door
    state: 'on'   # Optional, 'on' is the default value
    repeat:
      - 15
      - 30
      - 60
    can_acknowledge: true  # Optional, default is true
    skip_first: true  # Optional, false is the default
    notifiers:
      - ryans_phone
      - kristens_phone
```

Now the first message will be sent after a 15 minute delay, the second will be
sent 30 minutes after that, and a 60 minute delay will fall between every
following notification.
For example, if the garage door opens at 2:00, a notification will be
sent at 2:15, 2:45, 3:45, 4:45, etc., continuing every 60 minutes.

### {% linkable_title Message Templates %}

It may be desirable to have the alert notifications include information
about the state of the entity. [Templates](/docs/configuration/templating/)
can be used in the message or name of the alert to make it more relevant.
The following will show for a plant how to include the problem `attribute`
of the entity.

{% raw %}
```yaml
# Example configuration.yaml entry
alert:
  office_plant:
    name: Plant in office needs help
    entity_id: plant.plant_office
    state: 'problem'
    repeat: 30
    can_acknowledge: true
    skip_first: true
    message: "Plant {{ states.plant.plant_office }} needs help ({{ state_attr('plant.plant_office', 'problem') }})"
    done_message: Plant in office is fine
    notifiers:
      - ryans_phone
      - kristens_phone
```
{% endraw %}

The resulting message could be `Plant Officeplant needs help (moisture low)`.

The next example uses a template for the alert name.

{% raw %}
```yaml
alert:
  garage_door:
    name: Garage has been open for {{ relative_time(states.binary_sensor.garage.last_changed) }}
    done_message: Garage is closed
    entity_id: binary_sensor.garage
    state: 'on'
    repeat:
      - 30
      - 60
      - 120
    can_acknowledge: True
    skip_first: True
    notifiers:
      - ryans_phone
```
{% endraw %}

The resulting title of the alert could be `Garage has been open for 30 min`.

[template]: /docs/configuration/templating/
