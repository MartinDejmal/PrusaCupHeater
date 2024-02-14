# PrusaCupHeater

PrusaCupHeater is a project aiming to utilize spare / waste heatbedlets (heat bed tiles) from Prusa XL printer. 

The ultimate objective was to build a cup heater that can be powered from USB-C PD power supply and connected to Home Assistant. 

## Required parts

* Prusa XL Heat Bed Tile
* Wemos D1 Mini ESP8266 module (or similar)
* DS18B20 temperature sensor (or any alternative)
* power MOSFET/PWM breakout board
* USB-C PD Trigger with 20V out support
* DC-DC step-down converter (at least 24V input, 5V output)
* optional: 3 LED strip WS2812B
* 3D Printed box

### Purchase links

| Component/Module | CZ | International |
| --- | --- | --- |
|Prusa XL Heatbed tile|[prusa3d.com](https://www.prusa3d.com/cs/produkt/heatbed-tile/)||
|Wemos D1 Mini| [laskakit.cz](https://www.laskakit.cz/wemos-d1-mini-esp8266-wifi-modul/), [pajenicko.cz](https://pajenicko.cz/wemos-d1-mini-wifi-esp8266-esp-12f-microusb) | [Aliexpress](https://www.aliexpress.com/item/1005006473868001.html)|
|DS18B20|[laskakit.cz](https://www.laskakit.cz/dallas-digitalni-cidlo-teploty-ds18b20--to-92/), [pajenicko.cz](https://pajenicko.cz/digitalni-cidlo-teploty-dallas-ds18b20) | [Aliexpress](https://www.aliexpress.com/item/1005006105455874.html)|
|MOSFET module|[laskakit.cz](https://www.laskakit.cz/pwm-mosfet-modul-d4184--40vdc-50a/)|[Aliexpress](https://www.aliexpress.com/item/1005004768394186.html)|
|USB-C PD trigger|[laskakit.cz](https://www.laskakit.cz/laskakit-usb-c-pd-ch224k-prepinac-napajeciho-napeti/)||
|Step-down|[laskakit.cz](https://www.laskakit.cz/mikro-step-down-menic--nastavitelny/), [pajenicko.cz](https://pajenicko.cz/miniaturni-menic-napeti-step-down-4-dot-5v-24v-na-0-dot-8v17v-az-3a)|[Aliexpress](https://www.aliexpress.com/item/32807048132.html)|
|WS2812 LED strip|[laskakit.cz](https://www.laskakit.cz/led-pasek-neopixel-ws2812b-60led-m-ip65-5m-cerny/), [pajenicko.cz](https://pajenicko.cz/inteligentni-rgb-led-pasek-1m-ws2812-neopixel-60led-m-18w-m)||



## Assembly instructions
- wire up the modules as per diagram below
  - DS18B20 to D7 on Wemos D1, glue the sensor to the bottom of the heat bed tile
  - power MOSFET module to D1 on Wemos D1
- jumper the USB-C trigger to output 20V
  - wire 20V out from the PD trigger into the MOSFET switching part
  - connect power pins from MOSFET trigger into the heat bed tile

## Firmware instructions
- flash `tasmota-sensors` into the Wemos D1, you can use [Tasmota Web Installer](https://tasmota.github.io/install/)
- apply following template: `{"NAME":"PrusaCupHeater","GPIO":[0,0,0,0,0,224,0,0,0,1312,0,0,0,0],"FLAG":0,"BASE":18}`

## Home Assistant configuration
Prerequisite: Tasmota integration working with MQTT server
- if you want to control the Cup Heater via Home Assistant, you need to define a thermostat
- open your configuration.yaml and add following:
```
  climate:
    - platform: generic_thermostat
      unique_id: prusa_cup_heate
      name: PrusaCupHeater
      heater: switch.tasmota_2
      target_sensor: sensor.tasmota_ds18b20_temperature
      min_temp: 40
      max_temp: 120
```
Replace `switch.tasmota_2` and `sensor.tasmota_ds18b20_temperature` by relevant heater switch and temperature sensors entities in your setup. 
- change Logging interval on your Tasmota to lowest possible value (10s) - Configuration / Configure Logging / Telemetry Period


## Standalone operation configuration
It is possible to enable simple heating control using Tasmota Rules. To do this, open Tasmota Console and enter following commands:
- `Rule1 1`
- `Rule2 1`
- `Rule1 ON Tele-DS18B20#Temperature<55 DO Power1 1 ENDON`
- `Rule2 ON Tele-DS18B20#Temperature>65 DO Power1 0 ENDON`
You can adjust target temperature and temperature hysteresis limits as per your requirements. This setup has target 60°C while allowing +/-5°C hysteresis.
Note: berry scripting is not supported on ESP8266 devices.

## To do list
- include WS2812b LED strip control
- add berry script for autonomous operation
- add touch sensor to control operation
- add 128x32 OLED display with temperature and status reading
- add cup presence detection
