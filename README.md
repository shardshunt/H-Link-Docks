# H-Link-Docs
## Software

https://github.com/lumixen/esphome-hlink-ac

## Hardware

### Heatpump

Model Number: 

RAS-70YHA2

### Components
Connector used (JST PH2.0):

(does not fit without modification, I do not reccomend using, if purchasing use HY2.0):

https://www.aliexpress.com/item/1005007389108799.html

Logic level converter:

https://www.aliexpress.com/item/1005005984772131.html

Buck converter:

https://www.aliexpress.com/item/1005007031557776.html

ESP32 (CP2102):

https://www.aliexpress.com/item/1005006617407766.html

## Instructions:
### Step 1: Heat Pump Dissasembly:
 - turn of heatpump at master switch

Switch is typically located near the outdoor unit, ensure the green light is no longer illuminated on the indoor unit.

 - Remove three screws

These are located along the bottom side of the indoor unit with  plastic covers that just pull off.

![image](https://github.com/user-attachments/assets/fc4296af-6c1a-4fb5-a470-f3895c1baf4a)


 - Undo 6 clips

Using a flat srewdriver first undo to 4 along the top followed by the two under the filter cover.

![image](https://github.com/user-attachments/assets/e2d6db87-15da-46ed-9bfa-326974cd86db)

![image](https://github.com/user-attachments/assets/04a918cd-8d9c-4bc8-bdd5-ced297b5f823)

 - Remove the electronics housing cover

Remove Tape if present and undo three screws

- Local H-Link header

![image](https://github.com/user-attachments/assets/ce5c5abe-a243-49ce-a749-ca0390d74fd2)


### Modifying Connector:
If you have a PH2.0 you will need to file down both sides.

### Determin socket Orientation:
Using a multimeter find the wire that gives ~12V and ground (For me it was the following layout with 12V at the bottom).

![image](https://github.com/user-attachments/assets/774b9f2d-0b40-4f59-9ab1-4998cb9af368)

### Wiring
Follow the original diagram including using pin 16 and 17 for UART

![image](https://github.com/user-attachments/assets/f5737485-2d1a-44ac-94b3-189e04d45427)
![image](https://github.com/user-attachments/assets/4ce03b9a-939a-4480-a8fd-891162213673)

### Code

```
esphome:
  name: hitachi-h-link-controller
  friendly_name: Hitachi AC

esp32:
  board: esp32dev
  framework:
    type: esp-idf

# Enable logging
logger:

# Enable Home Assistant API
api:
  encryption:
    key: "encryption key"

ota:
  - platform: esphome

wifi:
  ssid: Wifi
  password: WifiPassword

uart:
  id: hitachi_bus
  tx_pin: GPIO17
  rx_pin: GPIO16
  baud_rate: 9600
  parity: ODD

external_components:
  - source:
      type: git
      url: https://github.com/lumixen/esphome-hlink-ac.git
      ref: 2025.5.1
    components: [hlink_ac]

climate:
  - platform: hlink_ac
    name: "LoungeAC"
    hvac_actions: true # set false if you don't need HVAC actions

switch:
  - platform: hlink_ac
    remote_lock:
      name: Remote lock
    beeper:
      name: Beeper

sensor:
  - platform: hlink_ac
    outdoor_temperature:
      name: Outdoor temperature

text_sensor:
  - platform: hlink_ac
    model_name:
      name: Model

number:
  - platform: hlink_ac
    auto_target_temperature_offset:
      name: Auto mode temperature offset
```

