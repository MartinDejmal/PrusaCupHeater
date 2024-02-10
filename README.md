# PrusaCupHeater

PrusaCupHeater is a project aiming to utilize spare / waste heatbedlets (heat bed tiles) from Prusa XL printer. 

The ultimate objective was to build a cup heater that can be powered from USB-C PD power supply and connected to Home Assistant. 

Required parts:

* Prusa XL Heat Bed Tile
* Wemos D1 Mini ESP8266 module (or similar)
* DS18B20 temperature sensor (or any alternative)
* power MOSFET/PWM breakout board
* USB-C PD Trigger with 20V out support
* DC-DC step-down converter (at least 24V input, 5V output)
* optional: 3 LED strip WS2812B
* 3D Printed box


Assembly instructions:
- wire up the modules as per diagram below
-- DS18B20 to D7 on Wemos D1, glue the sensor to the bottom of the heat bed tile
-- power MOSFET module to D1 on Wemos D1
- jumper the USB-C trigger to output 20V
-- wire 20V out from the PD trigger into the MOSFET switching part
-- connect power pins from MOSFET trigger into the heat bed tile

Firmware instructions:
- flash Tasmota-Sensors into the Wemos D1
- apply following template: {"NAME":"PrusaCupHeater","GPIO":[0,0,0,0,0,224,0,0,0,1312,0,0,0,0],"FLAG":0,"BASE":18}

Home Assistant configuration:
Prerequisite: Tasmota integration working with MQTT server
- if you want to control the Cup Heater via Home Assistant, you need to define a thermostat
- open your configuration.yaml and add following:

climate:
  - platform: generic_thermostat
    unique_id: prusa_cup_heater
    name: PrusaCupHeater
    heater: switch.tasmota_2
    target_sensor: sensor.tasmota_ds18b20_temperature
    min_temp: 40
    max_temp: 120

Replace switch.tasmota_2 and sensor.tasmota_ds18b20_temperature by valid entities in your setup. 


TO DO:
- include WS2812b LED strip control
- add berry script for autonomous operation
