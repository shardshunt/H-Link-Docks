# H-Link-Docs

This repository documents my implementation of Lumixen's excellent ESPHome custom component: [lumixen/esphome-hlink-ac](https://github.com/lumixen/esphome-hlink-ac).

> **Note:** AliExpress links may fluctuate in price and availability. These are not specific product endorsements.

---

## Hardware

### Heat Pump

- **Model Number:** RAS-70YHA2

### Components

- **Connector (JST PH2.0 requires modification):**  
  [AliExpress Link](https://www.aliexpress.com/item/1005007389108799.html)

- **Logic Level Converter:**  
  [AliExpress Link](https://www.aliexpress.com/item/1005005984772131.html)

- **Buck Converter:**  
  [AliExpress Link](https://www.aliexpress.com/item/1005007031557776.html)

- **ESP32 (CP2102):**  
  [AliExpress Link](https://www.aliexpress.com/item/1005006617407766.html)

---

## Instructions

### Step 1: Heat Pump Disassembly

1. **Power Off the Unit**  
   Turn off the heat pump at the main (master) switch. This is usually located near the outdoor unit. Ensure the green indicator light on the indoor unit is off.

2. **Remove Three Screws**  
   These screws are located along the bottom edge of the indoor unit. Plastic covers must be removed to access them.

   <img src="https://github.com/user-attachments/assets/fc4296af-6c1a-4fb5-a470-f3895c1baf4a" width="400"/>

3. **Release Six Clips**  
   Use a flathead screwdriver to release the four clips along the top, then two more under the filter cover.

   <img src="https://github.com/user-attachments/assets/e2d6db87-15da-46ed-9bfa-326974cd86db" width="400"/>  
   <img src="https://github.com/user-attachments/assets/04a918cd-8d9c-4bc8-bdd5-ced297b5f823" width="400"/>

4. **Remove Electronics Housing Cover**  
   Remove any tape if present and unscrew the three screws securing the electronics housing.

5. **Locate the H-Link Header**

   <img src="https://github.com/user-attachments/assets/ce5c5abe-a243-49ce-a749-ca0390d74fd2" width="400"/>

---

### Connector Modification

If using a PH2.0 connector, you will need to carefully file down both sides for it to fit.

---

### Determining Socket Orientation

Using a multimeter, identify the 12V and Ground pins. In my setup, the 12V pin was at the bottom:

<img src="https://github.com/user-attachments/assets/774b9f2d-0b40-4f59-9ab1-4998cb9af368" width="150"/>

---

### Wiring

Follow the reference wiring diagram. Use GPIO16 and GPIO17 for UART RX and TX respectively:

<img src="https://github.com/user-attachments/assets/f5737485-2d1a-44ac-94b3-189e04d45427" width="400"/>  
<img src="https://github.com/user-attachments/assets/4ce03b9a-939a-4480-a8fd-891162213673" width="400"/>

---

### ESPHome Configuration

```yaml
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
    hvac_actions: true  # Set to false if HVAC actions are not needed

switch:
  - platform: hlink_ac
    remote_lock:
      name: Remote Lock
    beeper:
      name: Beeper

sensor:
  - platform: hlink_ac
    outdoor_temperature:
      name: Outdoor Temperature

text_sensor:
  - platform: hlink_ac
    model_name:
      name: Model

number:
  - platform: hlink_ac
    auto_target_temperature_offset:
      name: Auto Mode Temperature Offset
```
