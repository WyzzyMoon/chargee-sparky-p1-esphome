# Chargee Sparky P1 — ESPHome Local Firmware

Replacement firmware for the **Chargee Sparky P1** energy monitor for fully offline local integration with ESPHome.\
\
While Chargee already offers a local API with their firmware and a HA integration, the Sparky itself still depends on an active internet connection. 

The device internally contains an fully flashable **ESPRESSIF ESP32-C3-MINI-1**, has native USB connected to the USB-C connector, and can be flashed directly without adding an external programmer.\
\
Tested with Sparky 3 v2 on Dutch ESMR 5.0 meter. Other hardware revisions have not been tested.

---

## What works:

- Reads the P1 smart-meter telegrams directly from the Sparky
- Parses DSMR/ESMR data locally
- Publishes energy, power, voltage, current and gas measurements to Home Assistant
- Uses the ESP32-C3's native USB connection for flashing
- Does not require the original Chargee cloud service
- Uses the RGB Led for status (Blue = booting, Green = working, Red = disconnected)
- Provides a  switch to disable the green LED.
- Powers purely from the P1 power, doesn't require additional USB-C power.

## Not working (yet):

The P1 output. Sparky provides a P1 daisy chain option. This is not just a passive direct connection to the output port. I have not fully figured out how it works and only need the reading functionality myself. But keep in mind that you'll currently lose this functionality. 

---

# Installation

## Requirements

- Chargee Sparky P1
- ESPHome
- Home Assistant (optional, but recommended)
- USB-C cable
- RJ12 cable

Before flashing custom firmware, I recommend making a backup of the original flash.\
Using esptool:

```bash
esptool --port /dev/ttyACM0 read_flash 0 ALL sparky-original.bin
```

The exact serial device name may differ on your system.\
\
Create an Empty Configuration in ESPHome Device Builder, replace its YAML with `sparky-p1.yaml`, then compile and install it over USB.\


---

# Home Assistant entities

The firmware currently exposes:

### Energy

- Energy Imported Tariff 1
- Energy Imported Tariff 2
- Energy Exported Tariff 1
- Energy Exported Tariff 2

### Power

- Power Import
- Power Export
- Power Import L1
- Power Export L1

### Electrical measurements

- Voltage L1
- Current L1

### Gas

- Gas Consumption

### Identification / information

- Meter Identification
- P1 Version
- Meter Timestamp
- Electricity Meter ID
- Electricity Tariff
- Gas Meter ID

### Status

- Normal Green LED

(this is to disable the always-on green led during normal operation) 

---

# Reverse-engineering notes

While I was able to reverse engineer the RX pin and the RGB pins, not everything is known yet

## Confirmed

- MCU: ESPRESSIF ESP32-C3-MINI-1
- GPIO18 = USB D−
- GPIO19 = USB D+
- GPIO5 = blue LED
- GPIO6 = green LED
- GPIO7 = red LED
- GPIO10 = P1 RX
- Baudrate = 115200&#x20;
- Normal UART polarity
- AMS1117 3.3 V regulator

## Still under investigation

- Exact function of the IC labled "U3" (I suspect a level shifter 5v > 3v)
- GPIO4 (suspected TX)

---

# Disclaimer

The information in this repository is provided for experimentation and research.\
Flashing replacement firmware probably voids your warranty \
Use at your own risk basically. 

---

## Credits

This firmware is not affiliated with or endorsed by Chargee in any way. \
But big shoudout to Chargee for making their device so open. The ESP was fully flash-able and in general they seem to have an open approach to their device given the local API and HA integrations they made themselves. 

Reverse Engineered with:

- ESPHome
- Esptools
- Home Assistant
- DSMR/P1 protocol docs
- Ghidra
- A multimeter and a lot of PCB tracing ;)
