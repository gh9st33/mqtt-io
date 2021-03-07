<!--
***************************************************************************************
DO NOT EDIT README.md DIRECTLY, IT'S GENERATED FROM README.md.j2 USING generate_docs.py
***************************************************************************************
-->

# MQTT IO

[![Discord](https://img.shields.io/discord/713749043662290974.svg?label=Chat%20on%20Discord&logo=discord&logoColor=ffffff&color=7389D8&labelColor=6A7EC2)](https://discord.gg/gWyV9W4)

Exposes general purpose inputs and outputs (GPIO), hardware sensors and serial devices to an MQTT server. Ideal for single-board computers such as the Raspberry Pi.

Visit the [documentation](https://flyte.github.io/mqtt-io/) for more detailed information.

## Supported Hardware

Hardware support is provided by specific GPIO, Sensor and Stream modules. It's easy to add support for new hardware and the list is growing fast.

### GPIO Modules

- Raspberry Pi GPIO (`raspberrypi`)
- Orange Pi GPIO (`orangepi`)
- PCF8574 IO chip (`pcf8574`)
- PCF8575 IO chip (`pcf8575`)
- PiFaceDigital 2 IO board (`piface2`)
- Beaglebone GPIO (`beaglebone`)
- Linux Kernel 4.8+ libgpiod (`gpiod`)

### Sensors

- LM75 i2c temperature sensor (`lm75`)
- DHT11 DHT22 AM2302 temperature/humidity sensor (`dht22`)
- BH1750 light level sensor (`bh1750`)
- DS18S20, DS1822, DS18B20, DS1825, DS28EA00, MAX31850K one-wire temperature sensors: (`ds18b`)
- HC-SR04 ultrasonic distance sensor (`hcsr04`)
- MCP3008 analog digital converter (`mcp3008`)
- BME280 temperature, humidity and pressure sensor (`bme280`)
- BME680 temperature, humidity and pressure sensor (`bme680`)

### Streams

- Serial port (`serial`)

## Installation

_Requires Python 3.6+_

`pip3 install mqtt-io`

## Execution

`python3 -m mqtt-io config.yml`

## Configuration Example

Configuration is written in a YAML file which is passed as an argument to the server on startup.

See the [full configuration documentation](https://github.com/flyte/pi-mqtt-gpio/wiki/Configuration) for details.

The following example will configure the software to do the following:

- Publish MQTT messages on the `home/input/doorbell` topic when the doorbell is pushed and released.
- Subscribe to the MQTT topic `home/output/port_light/set` and change the output when messages are received on it.
- Periodically read the value of the LM75 sensor and publish it on the MQTT topic `home/sensor/porch_temperature`.
- Publish any data received on the `/dev/ttyUSB0` serial port to the MQTT topic `home/serial/alarm_system`.
- Subscribe to the MQTT topic `home/serial/alarm_system/send` and send any data received on that topic to the serial port.

```yaml
mqtt:
  host: localhost
  topic_prefix: home

# GPIO
gpio_modules:
  # Use the Raspberry Pi built-in GPIO
  - name: rpi
    module: raspberrypi

digital_inputs:
  # Pin 0 is an input connected to a doorbell button
  - name: doorbell
    module: rpi
    pin: 0

digital_outputs:
  # Pin 1 is an output connected to a light
  - name: porch_light
    module: rpi
    pin: 1

# Sensors
sensor_modules:
  # An LM75 sensor attached to the I2C bus
  - name: lm75_sensor
    module: lm75
    i2c_bus_num: 1
    chip_addr: 0x48

sensor_inputs:
  # The configuration of the specific sensor value to use (LM75 only has temperature)
  - name: porch_temperature
    module: lm75_sensor

# Streams
stream_modules:
  # A serial port to communicate with the house alarm system
  - name: alarm_system
    module: serial
    device: /dev/ttyUSB0
    baud: 9600
```