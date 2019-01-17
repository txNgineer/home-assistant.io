---
layout: page
title: "HomeKit"
description: "Instructions on how to set up the HomeKit component in Home Assistant."
date: 2018-02-20 17:30
sidebar: true
comments: false
sharing: true
footer: true
ha_category: Voice
ha_release: 0.64
logo: apple-homekit.png
---

The `homekit` component allows you to forward entities from Home Assistant to Apple HomeKit, so they can be controlled from Apple's Home app and Siri. Please make sure that you have read the [considerations](#considerations) listed below to save you some trouble later. However if you do encounter issues, check out the [troubleshooting](#troubleshooting) section.

<p class="note">
  If you want to control `HomeKit` only devices with Home Assistant, check out the [HomeKit controller](/components/homekit_controller/) component.
</p>

<p class="note warning">
  It might be necessary to install an additional package:
  `$ sudo apt-get install libavahi-compat-libdnssd-dev`
</p>

```yaml
# Example configuration.yaml entry configuring HomeKit
homekit:
  filter:
    include_domains:
      - alarm_control_panel
      - light
      - media_player
  entity_config:
    alarm_control_panel.home:
      code: 1234
    light.kitchen_table:
      name: Kitchen Table Light
    lock.front_door:
      code: 1234
    media_player.living_room:
      feature_list:
        - feature: on_off
        - feature: play_pause
        - feature: play_stop
        - feature: toggle_mute
    switch.bedroom_outlet:
      type: outlet
```

{% configuration %}
  homekit:
    description: HomeKit configuration.
    required: true
    type: map
    keys:
      auto_start:
        description: Flag if the HomeKit Server should start automatically after the Home Assistant Core Setup is done. ([Disable Auto Start](#disable-auto-start))
        required: false
        type: boolean
        default: true
      port:
        description: Port for the HomeKit extension.
        required: false
        type: integer
        default: 51827
      name:
        description: Need to be individual for each instance of Home Assistant using the component on the same local network. Between `3` and `25` characters. Alphanumeric and spaces allowed.
        required: false
        type: string
        default: '`Home Assistant Bridge`'
      ip_address:
        description: The local network IP address. Only necessary if the default from Home Assistant does not work.
        required: false
        type: string
      safe_mode:
        description: Only set this parameter if you encounter issues during pairing. ([Safe Mode](#safe-mode))
        required: false
        type: boolean
        default: false
      filter:
        description: Filters for entities to be included/excluded from HomeKit. ([Configure Filter](#configure-filter))
        required: false
        type: map
        keys:
          include_domains:
            description: Domains to be included.
            required: false
            type: list
          include_entities:
            description: Entities to be included.
            required: false
            type: list
          exclude_domains:
            description: Domains to be excluded.
            required: false
            type: list
          exclude_entities:
            description: Entities to be excluded.
            required: false
            type: list
      entity_config:
        description: Configuration for specific entities. All subordinate keys are the corresponding entity ids to the domains, e.g., `alarm_control_panel.alarm`.
        required: false
        type: map
        keys:
          '`<ENTITY_ID>`':
            description: Additional options for specific entities.
            required: false
            type: map
            keys:
              name:
                description: Name of the entity to show in HomeKit. HomeKit will cache the name on the first run so a device must be removed and then re-added for any change to take effect.
                required: false
                type: string
              code:
                description: Code to `arm / disarm` an alarm or `lock / unlock` a lock. Only applicable for `alarm_control_panel` or `lock` entities.
                required: false
                type: string
                default: '`<No code>`'
              feature_list:
                description: Only for `media_player` entities. List of feature dictionaries to add for a given entity. Comparable to the platform schema.
                required: false
                type: list
                keys:
                  feature:
                    description: Name of the feature to add to the entity representation. Valid features are `on_off`, `play_pause`, `play_stop` and `toogle_mute`. The media_player entity must support the feature to be valid.
                    required: true
                    type: string
              type:
                description: Only for `switch` entities. Type of accessory to be created within HomeKit. Valid types are `faucet`, `outlet`, `shower`, `sprinkler`, `switch` and `valve`. HomeKit will cache the type on the first run so a device must be removed and then re-added for any change to take effect.
                required: false
                type: string
                default: '`switch`'
{% endconfiguration %}


## {% linkable_title Setup %}

To enable the HomeKit component in Home Assistant, add the following to your configuration file:

```yaml
# Example for HomeKit setup
homekit:
```

After Home Assistant has started, the entities specified by the filter are exposed to HomeKit if they are [supported](#supported-components). To add them:

1. Open the Home Assistant frontend. A new card will display the `pin code`.
1. Open the `Home` app.
2. Click `Add Accessory`, then select `Don't Have a Code or Can't Scan?` and choose the `Home Assistant Bridge`.
4. Confirm that you are adding an `Uncertified Accessory` by clicking on `Add Anyway`.
5. Enter the `PIN` code.
6. Follow the setup by clicking on `Next` and lastly `Done` in the top right-hand corner.
7. The `Home Assistant` Bridge and the Accessories should now be listed in the `Home` app.

After the setup is completed, you should be able to control your Home Assistant components through Apple's Home and Siri.

## {% linkable_title Move Home Assistant install %}

If you like to retain your HomeKit pairing through a move to a new Home Assistant device or installation, besides copying the configurations files you need to copy the `.homekit.state` file inside your configurations directory. Keep in mind though that the file is usually hidden by default, depending on your operating system.

Before you copy it, make sure to stop the old and new Home Assistant instances first entirely, otherwise it won't work.

## {% linkable_title Considerations %}

### {% linkable_title Accessory ID %}

Currently, this component uses the `entity_id` to generate a unique `accessory id (aid)` for `HomeKit`. The `aid` is used to identify a device and save all configurations made for it. This, however, means that if you decide to change an `entity_id` all configurations for this accessory made in the `Home` app will be lost.

### {% linkable_title Device Limit %}

The HomeKit guidelines only allow a maximum of 100 unique accessories (`aid`) per bridge. Be mindful of this when configuring the filter(s).

### {% linkable_title Persistence Storage %}

Unfortunately `HomeKit` doesn't support any persistent storage - only the configuration for accessories that are added to the `Home Assistant Bridge` are kept. To avoid problems, it is recommended to use an automation to always start `HomeKit` with at least the same entities setup. If for some reason some entities are not set up, their config will be deleted. (State unknown or similar will not cause any issues.)

A common situation might be if you decide to disable parts of the configuration for testing. Please make sure to disable `auto start` and `turn off` the `Start HomeKit` automation (if you have one).

## {% linkable_title Disable Auto Start %}

Depending on your setup, it might be necessary to disable `Auto Start` for all accessories to be available for `HomeKit`. Only those entities that are fully set up when the `HomeKit` component is started, can be added. To start `HomeKit` when `auto_start: False`, you can call the service `homekit.start`.

If you have Z-Wave entities you want to be exposed to HomeKit, then you'll need to disable auto start and then start it after the Z-Wave mesh is ready. This is because the Z-Wave entities won't be fully set up until then. This can be automated using an automation.

<p class='note'>
Please remember that you can only have a single `automation` entry. Add the automation to your existing automations.
</p>

{% raw %}
```yaml
# Example for Z-Wave
homekit:
  auto_start: False

automation:
  - alias: 'Start HomeKit'
    trigger:
      - platform: event
        event_type: zwave.network_ready
      - platform: event
        event_type: zwave.network_complete
      - platform: event
        event_type: zwave.network_complete_some_dead
    action:
      - service: homekit.start
```
{% endraw %}

For a general delay where your component doesn't generate an event, you can also do:

{% raw %}
```yaml
# Example using a delay after the start of Home Assistant
homekit:
  auto_start: False

automation:
  - alias: 'Start HomeKit'
    trigger:
      - platform: homeassistant
        event: start
    action:
      - delay: 00:05  # Waits 5 minutes
      - service: homekit.start
```
{% endraw %}

## {% linkable_title Configure Filter %}

By default, no entity will be excluded. To limit which entities are being exposed to `HomeKit`, you can use the `filter` parameter. Keep in mind only [supported components](#supported-components) can be added.

{% raw %}
```yaml
# Example filter to include specified domains and exclude specified entities
homekit:
  filter:
    include_domains:
      - alarm_control_panel
      - light
    exclude_entities:
      - light.kitchen_light
```
{% endraw %}

Filters are applied as follows:

1. No includes or excludes - pass all entities
2. Includes, no excludes - only include specified entities
3. Excludes, no includes - only exclude specified entities
4. Both includes and excludes:
   * Include domain specified
      - if domain is included, and entity not excluded, pass
      - if domain is not included, and entity not included, fail
   * Exclude domain specified
      - if domain is excluded, and entity not included, fail
      - if domain is not excluded, and entity not excluded, pass
      - if both include and exclude domains specified, the exclude domains are ignored
   * Neither include or exclude domain specified
      - if entity is included, pass (as #2 above)
      - if entity include and exclude, the entity exclude is ignored

## {% linkable_title Safe Mode %}

The `safe_mode` option should only be used (and only works) if you encounter issues during the pairing. ([Paring hangs - zeroconf error](#pairing-hangs---zeroconf-error)).

To use `safe_mode`, add the option to your `homekit` config:

```yaml
homekit:
  safe_mode: True
```

Restart your Home Assistant instance. If you don't see a `pincode`, follow the [guide](#deleting-the-homekitstate-file) here. Now you should be able to pair normally.

<p class="note warning">
To avoid any errors, after you have successfully paired your Home Assistant Bridge, remove the `safe_mode` option from your config and restart Home Assistant.
</p>

## {% linkable_title Supported Components %}

The following components are currently supported:

| Component | Type Name | Description |
| --------- | --------- | ----------- |
| alarm_control_panel | SecuritySystem | All security systems. |
| automation / input_boolean / remote / scene / script | Switch | All represented as switches. |
| binary_sensor | Sensor | Support for `co2`, `door`, `garage_door`, `gas`, `moisture`, `motion`, `occupancy`, `opening`, `smoke` and `window` device classes. Defaults to the `occupancy` device class for everything else. |
| climate | Thermostat | All climate devices. |
| cover | GarageDoorOpener | All covers that support `open` and `close` and have `garage` as their `device_class`. |
| cover | WindowCovering | All covers that support `set_cover_position`. |
| cover | WindowCovering | All covers that support `open_cover` and `close_cover` through value mapping. (`open` -> `>=50`; `close` -> `<50`) |
| cover | WindowCovering | All covers that support `open_cover`, `stop_cover` and `close_cover` through value mapping. (`open` -> `>70`; `close` -> `<30`; `stop` -> every value in between) |
| device_tracker | Sensor | Support for `occupancy` device class. |
| fan | Fan | Support for `on / off`, `direction` and `oscillating`. |
| light | Light | Support for `on / off`, `brightness` and `rgb_color`. |
| lock | DoorLock | Support for `lock / unlock`. |
| media_player | MediaPlayer | Represented as a series of switches which control `on / off`, `play / pause`, `play / stop`, or `mute` depending on `supported_features` of entity and the `mode` list specified in `entity_config`. |
| sensor | TemperatureSensor | All sensors that have `Celsius` or `Fahrenheit` as their `unit_of_measurement` or `temperature` as their `device_class`. |
| sensor | HumiditySensor | All sensors that have `%` as their `unit_of_measurement` and `humidity` as their `device_class`. |
| sensor | AirQualitySensor | All sensors that have `pm25` as part of their `entity_id` or `pm25` as their `device_class` |
| sensor | CarbonMonoxideSensor | All sensors that have `co` as their `device_class` |
| sensor | CarbonDioxideSensor | All sensors that have `co2` as part of their `entity_id` or `co2` as their `device_class` |
| sensor | LightSensor | All sensors that have `lm` or `lx` as their `unit_of_measurement` or `illuminance` as their `device_class` |
| switch | Switch | Represented as a switch by default but can be changed by using `type` within `entity_config`. |
| water_heater | WaterHeater | All water_heater devices. |

## {% linkable_title Troubleshooting %}

### {% linkable_title Deleting the `.homekit.state` file %}

The `.homekit.state` file can be found in the configurations directory. You might need to enable `view hidden files` to see it.

 1. **Stop** Home Assistant
 2. Delete the `.homekit.state` file
 3. **Start** Home Assistant

### {% linkable_title Errors during pairing %}

If you encounter any issues during pairing, make sure to:

 1. **Stop** Home Assistant
 2. Delete the `.homekit.state` file
 3. Edit your configuration (see below)
 4. **Start** Home Assistant

```yaml
logger:
  default: warning
  logs:
    homeassistant.components.homekit: debug
    pyhap: debug

homekit:
  filter:
    include_entities:
      - demo.demo
```

#### {% linkable_title PIN doesn't appear as persistent status %}

You might have paired the `Home Assistant Bridge` already. If not, delete the `.homekit.state` file ([guide](#deleting-the-homekitstate-file)).

#### {% linkable_title `Home Assistant Bridge` doesn't appear in the Home App (for pairing) %}

This is often setup and network related. Make sure to check the other issues below as well, but things that might work include:
- Check your router configuration
- Try with WIFI **and** LAN
- Change the default [port](#port)

Remember that the iOS device needs to be in the same local network as the Home Assistant device for paring.

#### {% linkable_title `Home Assistant Bridge` doesn't appear in the Home App (for pairing) - Docker %}

Set `network_mode: host`. If you have further problems this [issue](https://github.com/home-assistant/home-assistant/issues/15692) might help.

#### {% linkable_title `Home Assistant Bridge` doesn't appear in the Home App (for pairing) - VirtualBox %}

Configure the network mode as `networkbridge`. Otherwise the Home Assistant Bridge won't be exposed to the network.

#### {% linkable_title Pairing hangs - zeroconf error %}

Pairing eventually fails, you might see and an error message `NonUniqueNameException`. Add the `safe_mode` option to your config, see [safe_mode](#safe-mode).

#### {% linkable_title Pairing hangs - only works with debug config %}

Pairing works fine when the filter is set to only include `demo.demo`, but fails with normal config. See [specific entity doesn't work](#specific-entity-doesnt-work)

#### {% linkable_title Pairing hangs - no error %}

Make sure that you don't try to add more than 100 accessories, see [device limit](#device-limit). In rare cases, one of your entities doesn't work with the HomeKit component. Use the [filter](#configure-filter) to find out which one. Feel free to open a new issue in the `home-assistant` repo, so we can resolve it.

#### {% linkable_title Duplicate AID found when attempting to add accessory %}

Two of your entities share the same `entity_id`. Either resolve this or configure the [filter](#configure-filter) to exclude them.

### {% linkable_title Issues during normal use %}

#### {% linkable_title Some of my devices don't show up - Z-Wave / Discovery %}

See [disable auto start](#disable-auto-start)

#### {% linkable_title My entity doesn't show up %}

Check if the domain of your entity is [supported](#supported-components). If it is, check your [filter](#configure-filter) settings. Make sure the spelling is correct, especially if you use `include_entities`.

#### {% linkable_title HomeKit doesn't work on second Home Assistant instance %}

To use the HomeKit component with to different Home Assistant instances on the same local network, you need to set a custom name for at least one of them. [config/name](#name)

#### {% linkable_title Specific entity doesn't work %}

Although we try our best, some entities don't work with the HomeKit component yet. The result will be that either pairing fails completely or all Home Assistant accessories will stop working. Use the filter to identify which entity is causing the issue. It's best to try pairing and step by step including more entities. If it works unpair and repeat until you find the one that is causing the issues. To help others and the developers, please open a new issue here: [home-assistant/issues/new](https://github.com/home-assistant/home-assistant/issues/new?labels=component: homekit)

#### {% linkable_title Accessories are all listed as not responding %}

See [specific entity doesn't work](#specific-entity-doesnt-work)

#### {% linkable_title Accessory not responding - after restart or update %}

See [device limit](#device-limit)

#### {% linkable_title Accessory not responding - randomly %}

Unfortunately, that sometimes happens at the moment. It might help to close the `Home` App and delete it from the cache. Usually, the accessory should get back to responding after a few minutes at most.

#### {% linkable_title Accessories not responding / behaving unusual - Upgrade from `0.65.x` %}

To fix this, you need to unpair the `Home Assistant Bridge`, delete the `.homekit.state` file ([guide](#deleting-the-homekitstate-file)) and pair it again. This should only be an issue if you're upgrading from `0.65.x` or below.
