# Light Sensor with ESP32

## Description

Messasure light and pushes values to MQTT broker.

## Features

- Measure light value in lux
- MQTT support (for Node-Red or Home Assistant)
- easy integration in own WiFi network (Hotspot settings-page)
- support deep sleep to save energy

## Parts

- [ESP32 Development Board\*](https://www.banggood.com/custlink/mKDYaR8Uuc)
- light sensor (BH1750)

\* affiliate links that help to support my projects

## Sketch

![sketch](/docs/sketch_bb.png)

## Wiring

### Light sensor (BH1750)

| Pin on Sensor | description  | Pin on ESP32     |
| ------------- | ------------ | ---------------- |
| GND           | GND          | GND              |
| VCC           | Power (3.3V) | 3V3              |
| SDA           | UART RXD     | GPIO21 (I2C SDA) |
| SCL           | UART TXD     | GPIO22 (I2C SCL) |

## Setup

- connect all parts to the ESP32
- connect power
- the ESP32 starting an own Wifi access point named "light-sensor-{device ID}"
- connect to this Wifi with your smartphone
- use `waaatering` for Wifi password
- you will redirected to a config page
- insert your Wifi and MQTT broker credentials
- click on "save"
- the ESP32 will restarting and connect to your MQTT broker
- after about 60 seconds should you should see a new topic in your MQTT broker `light-sensor/{device ID}/out/info` with a lot of information in the payload

## MQTT API

The whole module is controllable via MQTT protocol. So it's easy to integrate in existing SmartHome systems (like Home Assistant or Node-Red).

### Incoming commands

Topic: light-sensor/`{device ID}`/in/`{command}`

| command    | description                                                                                | payload              |
| ---------- | ------------------------------------------------------------------------------------------ | -------------------- |
| info       | Send info via MQTT topic `light-sensor/out/info` package                                   | -                    |
| sleep      | Start deep-sleep for specific duration (in seconds!)                                       | { duration: number } |
| hard-reset | Reset config with WiFi and MQTT settings and start internal hotspot to reconfigure device. | -                    |

### Outcoming commands

Topic: light-sensor/`{device ID}`/out/`{command}`

| command | description                              | payload                                |
| ------- | ---------------------------------------- | -------------------------------------- |
| info    | status info                              | complex JSON. See "info-state" chapter |
| sleep   | was send if sytem enter deep-sleep       | { duration: number }                   |
| wakeup  | was send if sytem wakeup from deep-sleep | reason "timer"                         |

#### Info state

| field               | description                                             | type   |
| ------------------- | ------------------------------------------------------- | ------ |
| version             | version number of module firmware                       | string |
| light               | light value (in lux)                                    | number |
| system.deviceId     | Unique ID of the device. Will used also in MQTT topics. | string |
| system.freeHeap     | free heap memory of CPU                                 | number |
| network.wifiRssi    | wifi signal strength (RSSI)                             | number |
| network.wifiQuality | quality of signal strength (value between 0 and 100%)   | number |
| network.wifiSsid    | SSID of connected wifi                                  | string |
| network.ip          | ip address of the module                                | string |
