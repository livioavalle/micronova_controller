# micronova_controller ![ESP8266](https://img.shields.io/badge/ESP-8266-000000.svg?longCache=true&style=flat&colorA=CCCC33) ![ESP32](https://img.shields.io/badge/ESP-32-000000.svg?longCache=true&style=flat&colorA=FF3500)
<a href="https://www.tindie.com/products/philibertc/pellet-heater-controller/">
  <img src="https://user-images.githubusercontent.com/57588282/131249215-e0877966-c181-49fa-ae90-59f0a95ad292.png" alt="I sell on Tindie" width=150 />
</a>
<a href="https://www.paypal.com/donate?hosted_button_id=Q48QBGGQTVGTG">
  <img src="https://raw.githubusercontent.com/stefan-niedermann/paypal-donate-button/master/paypal-donate-button.png" alt="Donate with PayPal" width=150 />
</a>

**Kits are available on Tindie!**  
**_Currently out of stock. V2 will be in stock soon!_**  
**Here is an overview of the additions:**

- **possibility to reset the settings with a flat screwdriver**
- **IO footprints are now designed to accept screw terminals**
- **D0 and RST are connected for deep sleep**
- **a row of pins has been added to access unused pins**

This Arduino program allows to control easily via MQTT a pellet stove equipped with a MicroNova mainboard.

## Working with
all stoves equipped with MicroNova mainboard.
<details>
<summary><b>Known brands</b></summary>

- AMG S.p.A. (Artel, Kalor, Tepor, Foco, Adler)
- Anselmo Cola
- Boreal
- Bronpi
- Cadel
- Clam
- Corisit S.r.l. (Lincar, Vulcania, Arce)
- Ecoteck
- EL.FIRE
- EOSS
- EvaCalor
- Fontana Calore
- Fonte Flamme
- Galletti
- Globe-Fire
- Jolly Mec
- Karmek
- Kepo
- Klover
- Laminox
- LMX
- La Nordica S.p.A (Extraflame, Dal Zotto)
- Lorflam
- MCZ (Brisach, Cadel, FreePoint, Pegaso, Red)
- Moretti Design
- Nordic Fire
- Sicalor
- Stufe a pellet Italia
- Tecnoflam
- Termoflam
- Thermoflux
- TS
- Ungaro
- Zibro
</details>

## Confirmed working with
- RED Loto
- MCZ Kaika/Face
- FreePoint Sharp
- Anselmo Cola Aloe
- Kalor Compact (boiler)

## What you need
A [kit](https://www.tindie.com/products/philibertc/pellet-heater-controller/) or:
- An ESP8266 (I recommend the D1 Mini). (approx. 4€)
- 3 PC817. (less than 2€ for 20pcs)
- 3 510 Ohms resistors. (less than 2€ for 100pcs)
- That's all!

## The circuit
![image](https://user-images.githubusercontent.com/57588282/128608590-39b0e0ff-b224-42bd-bb20-524a78f282f6.png)

`Enable_RX` is D2 (GPIO4) on ESP8266 or GPIO35 on ESP32.  
`RX` is D3 (GPIO0) on ESP8266 or GPIO32 on ESP32.  
`TX` is D4 (GPIO2) on ESP8266 or GPIO33 on ESP32.

The optocouplers are used to convert between 3.3V and 5V logic and also to protect the pellet stove and the ESP from each other in case of problem.

## Stove's mainboard pinout
There is a 4 pin connector (CN13 or SERIALE) with 5V, 20V, Serial, GND:

<details>
<summary><b>Schematics</b></summary>

<p align="center">
  <img src="https://user-images.githubusercontent.com/57588282/131531945-22b093b1-1757-4a82-a29e-b54d00d0ac6f.png" />
</p>
<table><tr>
<td> <img src="https://user-images.githubusercontent.com/57588282/133886798-e9a35942-9dad-4a9d-a861-0ae5c7e25d59.png" alt="Connector" style="width: 250px;"/> </td>
<td> <img src="https://user-images.githubusercontent.com/57588282/133889340-4191e813-1f91-44b8-8232-8bea3f137c58.png" alt="Drawing" style="width: 250px;"/> </td>
</tr></table>
  
</details>

## MicroNova protocol
Explained [here](https://ridiculouslab.altervista.org/en/micronova-protocol-converter/) under "Micronova protocol".

## Uploading firmware
### With esptool or NodeMCU PyFlasher
- Download the hex file corresponding to your board and your language in the [releases section](https://github.com/philibertc/micronova_controller/releases),
- Connect your ESP to your computer,
- Flash your ESP with that hex file (for esptool: `esptool write_flash *.hex`).
### With Arduino IDE
- Install ESP cards with the board manager (custom URLs: `http://arduino.esp8266.com/stable/package_esp8266com_index.json,https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json`),
- Install WifiManager and PubSubClient libraries,
- Copy the content of `micronova_controller.ino`,
- Connect your ESP to your computer and flash.

## Configure WiFi and the MQTT broker URL
- Connect to the `Pellet heater controller` WiFi network.
- Go to 192.168.4.1.
- Setup your WiFi credentials.
- Setup `mqtt_server` (the IP of your MQTT broker).
- Optionally adapt `mqtt_port` if you aren't using 1883.
- Optionally adapt `base_topic` if you have multiple stoves.
- Optionally setup `mqtt_user` and `mqtt_pass`.
- Optionally set `hydro_mode` from `0` to `1` if your pellet stove has the boiler feature.

## MQTT details
First of all, if you use Home Assistant read [this paragraph](#home-assistant).
### The different topics
- The `mqtt_topic`, it's the common part between all the topics (default: `micronova`).
- The `pong_topic`, message "Powered up" sent every 25 seconds (`mqtt_topic`**`/pong`**).
- The `state_topic`, this is the topic containing the detailed status of the stove (`mqtt_topic`**`/state`**).
- The `onoff_topic`, this is the topic informing if the stove is turned off or on (`mqtt_topic`**`/onoff`**).
- The `ambtemp_topic`, this is the topic where you can read the ambient temperature (`mqtt_topic`**`/ambtemp`**).
- The `fumetemp_topic`, this is the topic on which you can read the temperature of the fumes (`mqtt_topic`**`/fumetemp`**).
- The `flame_topic`, this is the topic on which you can read the power of the flame (in percent) (`mqtt_topic`**`/flamepower`**).
- The `watertemp_topic`, this is the topic on which you can read the water temperature (only if you have a hydro pellet stove) (`mqtt_topic`**`/watertemp`**).
- The `waterpres_topic`, this is the topic on which you can read the water pressure in bars (only if you have a hydro pellet stove) (`mqtt_topic`**`/waterpres`**).
- The `in_topic`, it is the topic allowing to control the stove (`mqtt_topic`**`/intopic`**).

### Controlling the stove
- To light the stove send `ON` to `in_topic`, __without retain flag__.
- To turn off the stove send `OFF` to `in_topic`, with or without retain flag.
- To force extinction of the stove send `force` to `in_topic`, __without retain flag__.
- To put the ESP into deep sleep send `Sleep` to `in_topic`, with retain flag.
- To wake up the ESP send `Wake` to `in_topic`, with retain flag. (When the ESP is in deep sleep, it wakes up every 5 minutes to check if the `Wake` message has been received.)

## Reset all settings
If you want to reset all the settings here is what you can do:
- send `reset` to `in_topic` or
- connect the `RESET_PIN` (D5 (GPIO14) on ESP8266 or GPIO25 on ESP32) to GND (this is not the RST pin of the ESP) or
- `esptool erase_flash` but this will also delete the program.
## Home Assistant
Here is what to add to `configuration.yaml`:

```yaml
switch:
  - platform: mqtt
    name: ""    #E.g. "Living room"
    state_topic: "mqtt_topic/onoff"    #You have to edit that
    command_topic: "mqtt_topic/intopic"    #Also that
    payload_on: "ON"
    payload_off: "OFF"
    state_on: "ON"
    state_off: "OFF"
    retain: false
    optimistic: false
    qos: 0
    icon: mdi:fire
```

```yaml
sensor:
  - platform: mqtt
    name: ""    #E.g. "Stove controller power state living room"
    state_topic: "mqtt_topic/pong"
    qos: 0
    icon: mdi:power
  - platform: mqtt
    name: ""    #E.g. "Living room"
    state_topic: "mqtt_topic/ambtemp"
    qos: 0
    unit_of_measurement: "ºC"
    icon: mdi:thermometer
  - platform: mqtt
    name: ""    #E.g. "Fumes temp living room"
    state_topic: "mqtt_topic/fumetemp"
    qos: 0
    unit_of_measurement: "°C"
    icon: mdi:thermometer
  - platform: mqtt
    name: ""    #E.g. "Stove state living room"
    state_topic: "mqtt_topic/state"
    qos: 0
    icon: mdi:fire-alert
  - platform: mqtt
    name: ""    #E.g. "Flame power living room"
    state_topic: "mqtt_topic/flamepower"
    qos: 0
    unit_of_measurement: "%"
    icon: mdi:fire
    #Uncomment below if you own an hydro stove
#  - platform: mqtt
#    name: ""    #E.g. "Water temp living room"
#    state_topic: "mqtt_topic/watertemp"
#    qos: 0
#    unit_of_measurement: "ºC"
#    icon: mdi:coolant-temperature
#  - platform: mqtt
#    name: ""    #E.g. "Water pressure living room"
#    state_topic: "mqtt_topic/waterpres"
#    qos: 0
#    unit_of_measurement: "bar"
#    icon: mdi:gauge
```

## Thanks
Thanks to @pedrware for the photo of the mainboard.  
Thanks to [ridiculouslab](https://ridiculouslab.altervista.org/en/micronova-protocol-converter/) for his analysis of the serial protocol.

## License
<a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-nd/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/4.0/">Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International License</a>.

## Disclaimer
THIS SOFTWARE IS PROVIDED "AS IS" AND ANY EXPRESSED OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE REGENTS OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
