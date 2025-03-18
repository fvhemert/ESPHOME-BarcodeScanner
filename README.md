# ESPHOME-BarcodeScanner
<img src="https://github.com/user-attachments/assets/7599e981-eb70-4ef0-9e64-8ed92874b929" width=50% height=50%><br>

**Introduction**<br>
Inspired by the youtube videos from _Matt Fryer_ and _SmartHome Yourself_ I decided to start looking into creating an automated shoppinglist in Home Assistant.
The initial setup as described here is intended to learn, further define the requirements and most important, see if it brings value to our every day life.

The first iteration of this project will support the following scenario:
- Before a consumed product is discarded, its barcode is scanned by a barcode scanner that is located on the kitchen counter
- After scanning the barcode, an attempt is made to automatically resolve the code to a product name
- If the product name is successfully resolved, the product is automatically added to the Home Assistant shopping list and the product information is locally cached
- If the product name cannot be resolved, the user will receive a notification on his/her mobile phone, this notification will include a button that opens up a webpage to manually add the product information.
- The manually updated product information will be stored in the local cache for future use and the product will be added to the shopping list.
- If the product is already on the shopping list, it will not be added a second time.
- If it is time to go to the store, pushing a button on top of the scanner will trigger the printout of a formatted shoppinglist with all previously added products.
- Returning from the store, there are two option to clear the shopping list: 1) a button on the dashboard that clears the entire list  at once 2) a separte dashboard that allows to remove individual items one by one.
- All important interaction with the barcode scanner will be confirmed by using TTS and the kitchen SONOS mediaplayer.

**Note:**<br>
The orginal project leveraged the MEALIE integration to synchronize with MEALIE, a locally installed recipe manager and meal planner. I have decided to abandon this additional integration and only use the native Home Assistant Shopping list integration.
In case of scanning a product that is already on the shopping list, the MEALIE integration will modify the shopping list entry by adding a number prefix or increasing the allready existing one. This functionality will no longer be availble and has been replaced by logic that will eliminate adding duplicate entries, this logic at this time does not include the number prefix.

**Highlevel overview**<br>
The illustration below depicts the highlevel implementation of the Barcode Scanner implmentation
![image](https://github.com/user-attachments/assets/3128395e-f256-4172-bc37-2f82b9d9ae85)


# Components<br>
**_GM67 Barcode scanner module_**<br>
<img src="https://github.com/user-attachments/assets/bf544f15-44c3-4601-a529-7d63a8ee7e7b" width=10% height=10%><br>
Barcode scanner module that supports a wide variety of barcode types and can be connceted to an ESP device through a TTL serial connection.
The device can be aquired through Amazon or Aliexpress, proct information can be found at http://www.growbarcode.com/productinfo/888736.html <br>
*Note:* I have also experimented with the GROW GM60 stainless steel scanner module and found the module to be less reliable in low light conditions. This module does not have a collimation light and also does not come with a helper (white) led. 

**_Thermal printer_**<br>
<img src="https://github.com/user-attachments/assets/f2c6cd00-05cf-4cc0-b8a0-5b0033673be7" width=10% height=10%><br>
This low cost thermal printer is used to printout the shopping list. It is connected to the ESP device through the TTL serial connection, and relies on Home Assistant for output formatting. 
The printer supports both a TTL and USB serial connection but only the TTL output is used.
The printer can be ordered through Aliexpress: https://www.aliexpress.com/item/1005005604950041.html?spm=a2g0o.order_list.order_list_main.16.385c1802P4u6IT

**_SSD1306 OLED display_**<br>
<img src="https://github.com/user-attachments/assets/21e827e9-b19b-46eb-a03a-e741e661d05c" width=10% height=10%><br>
A small, low cost, I2C Display 128 x 64 Pixel 0,96 inch display with white pixels. The display shows the operating mode of the scanner and the information of the product scanned.
The display an be ordered from Amzon, Aliexpress and other sources, make sure you select the I2C version, there is also a version that supports SPI.

**_Buttons_**<br>
<img src="https://github.com/user-attachments/assets/4897bea9-8253-4ac0-b9f0-2e141c04ad2a" width=7% height=7%><br>
Simple push button switch that inludes a led. The switches are used to manually turn on and off the barcode scanner and trigger the printout of the sshopping list.<br>

**_ESP8266_**<br>
<img src="https://github.com/user-attachments/assets/ce8d8eb2-6eae-4f33-b7c5-548f9fe575a6" width=10% height=10%><br>
The core processor for the barcode scanner, managed from ESPHOME in Home Assistant.
The Lolin NodeMCY V3 board is configured to connect over serial to the barcode scanner and thermal printer, I2C to the display and 4 GPIO pins are used to read the status of the push button switches and control the LED's.

**_Mobile Phone_**<br>
If the barcode of a scanned item cannot be resolved to a product name, a notificiation is sent to the mobile phone of the user. This actionable notification includes a button that, when clicked, will open a dedicated dashboard page that allows the user to type in the missing information.
To enable this functionality, the Home Assistant mobile client is installed on the mobile phone.

**_Touchscreen_**<br>
The shopping list and product update page is also available on the various touchscreens in the house. These toucscreens have the FullyKiosk browser installed, allowing them to switch to the shoppinglist dashboard pages when new articles are scanned.

**_Meanwell powersupply RS-15-5, 15W 5V_**<br>
<img src="https://github.com/user-attachments/assets/a98e8c54-98c4-4d25-914c-431735db9a35" width=10% height=10%><br>
The thermal printer requires a stable 5V power supply rated at 3A, testing with various external 3A adapters showed that they are not supplying enough power to operate the printer. This is why a Meanwell powersupply is used.

**_3D printed enclosure_**<br>
Two enclosures designed in Fusion360 to accomodate the Barcode scanner.
The first enclosure (light grey) uses the GM60 scanner, the OLED display and an external powersupply, the second enclosure (black) is build around the GM67 scanner module and also includes a thermal printer and powersupply.

**_Home Assistant_**<br>
Home Assistant is the core orchestrator of the Shoppinglist automation, it uses the following (custom) integrations:

1. ESPHOME - to create a YAML configuration file and install directly onto the ESP8266. https://esphome.io/
2. SONOS - to play the Text to Speech notifications. https://www.home-assistant.io/integrations/sonos
3. FULLY KIOSK BROWSER - Show shoppinglist dashboards on wall mounted tablets. https://www.home-assistant.io/integrations/fully_kiosk
4. PYSCRIPT - integration to use Python functions and scripts in homeassistant https://github.com/custom-components/pyscript
5. SHOPPING LIST - keep track of shopping list items in Home Assistant https://www.home-assistant.io/integrations/shopping_list

# High level installation steps<br>
1. **Python scripts:** the scripts provided by Matt Fryer are installed "as-is" following the guidance provided on his GitHub page. A copy of the Python scripts can be found in the **pyscript** folder in this repository. Make sure to also install the Python custom integration in Home Assistant.
2. **ESP8266:** the **esphome** folder in this repository contains the modified/updated yaml file that was originally created by Matt Fryer. Install it to the ESP device using the ESPHOME plaform.
3. **Home Assistant:** use the yaml code provided in the **ha_automation** and **ha_dashboards** folders to build your HA automations. Update the entity names to match your installation.

# Repository content<br>
- **ESPHome**  Updated yaml file for the ESP8266 device. (SCANNER-01 uses the GM60 scanner, SCANNER-02 supports the GM67 scanner and thermal printer)<br>
- **ha_automation** sample automations to use as a starting point for building the shopping list automation<br>
- **hardware doumentation** product information and manuals for the various components that were used.<br>
- **pyscript** a 1:1 copy of the pyscripts created by Matt Fryer<br>
- **input-sources** misc information used during the build<br>

# ToDo<br>
- Establish 2-way communication with the printer to enable "paper out" messages.
- Implement an easy way to clear the shopping list
- Extend the functionality with an inventory system that keeps track of product stock

# Credits<br>
The solution above was built using components and code from both:<br>
Matt Fryer - https://github.com/MattFryer/HA-Mealie-Barcode-Scanner<br>
Smarthome yourself  - https://github.com/SmartHome-yourself/barcode-scanner-for-esphome<br>

Their excellent videos and guidance were a great inspiration and helped me along the way
