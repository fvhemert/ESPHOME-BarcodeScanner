# ESPHOME-BarcodeScanner

**Introduction**<br>
Triggered by the youtube videos from _Matt Fryer_ and _SmartHome Yourself_ I decided to start looking into creating an automated shoppinglist in Home Assistant.
The initial setup as described here is intended to learn, further define the requirements and most important, see if it brings value to our every day life.

The first iteration of this project will support the following scenario:
- Before a consumed product is discarded, its barcode is scanned on by a scanner that is located on the kitchen counter
- After scanning the barcode an attempt is made to automatically resolve the code to a product name
- If the product name is successfully resolved, the product is automatically added to the shopping list
- If the product name cannot be resolved, the user will receive a notification on his/her mobile phone, the notification inlcudes a button that opens up a webpage to manually add the product information.
- The manually updated product information will be stored for future use and the product will be added to the shopping list.
- If it is time to go to the store, pushing a button on top of the scanner will trigger the printout of a nicely formatted shoppinglist containg all previously added products.

The shopping list synchronizes with MEALIE, a locally installed recipe manager and meal planner.

**Highlevel overview**
The illustration below depicts the highlevel implementation of the Barcode Scanner implmentation
![image](https://github.com/user-attachments/assets/8a0614ef-5037-4cc1-a282-e38d920bc123)

# Components:

**_GM67 Barcode scanner module_**<br>
<img src="https://github.com/user-attachments/assets/bf544f15-44c3-4601-a529-7d63a8ee7e7b" width=10% height=10%><br>
Barcode scanner module that supports a wide variety of barcode types and can be connceted to an ESP device through a TTL serial connection.
The device can be aquired through Amazon or Aliexpress, proct information can be found at http://www.growbarcode.com/productinfo/888736.html 
Note: I have also used the GROW GM60 stainless steel scanner module and found the module to be less reliable in low light conditions. This module does have a collimation light and also does not come with a helper (white) led.

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

**_Voice Assistant_**<br>
<img src="https://github.com/user-attachments/assets/a7a821b9-712e-4503-9b37-a9483f083062" width=7% height=7%><br>
Amazon Alexa together with the Home Assistant integration and Text to Speech engine is used to provide speech feedback everytime a product is scanned. It will speak out the name of the product that was scanned or if a product is not recognized, urge the user to manually add the product details.

**_Mobile Phone_**<br>
If the barcode of a scanned item cannot be resolved to a product name, a notificiation is sent to the mobile phone of the user. This actionable notification includes a button that, when clicked, will open a dedicated dashboard page that allows the user to type in the missing information.
To enable this functionality, the Home Assistant mobile client is installed on the mobile phone.

**_Touchscreen_**<br>
The shopping list and product update page is also available on the various touchscreens in the house. These toucscreens have the FullyKiosk browser installed, allowing them to switch to the shoppinglist dashboard pages when new articles are scanned.

**_Home Assistant_**<br>
Home Assistant is the core orchestrator of the Shoppinglist automation, it uses the following (custom) integrations:

1. ESPHOME - to create a YAML configuration file and install directly onto the ESP8266. https://esphome.io/
2. ALEXA MEDIA PLAYER - to create Text to Speech notifications. https://github.com/alandtse/alexa_media_player/wiki 
3. FULLY KIOSK BROWSER - Show shoppinglist dashboards on wall mounted tablets. https://www.home-assistant.io/integrations/fully_kiosk
4. MEALIE - SHopping list integration. https://www.home-assistant.io/integrations/mealie
5. PYSCRIPT - integration to use Python functions and scripts in homeassistant https://github.com/custom-components/pyscript
6. SHOPPING LIST - keep track of shopping list items in Home Assistant https://www.home-assistant.io/integrations/shopping_list

**_Mealie_**<br>
A self-hosted recipe manager and meal planner with a RestAPI backend and a reactive frontend application built in Vue for a pleasant user experience for the whole family. 
For more information refer to: https://docs.mealie.io/

**ToDo**
- Establish 2-way communication with the printer to enable "paper out" messages.
- Implement an easy way to clear the shopping list
- Extend the functionality with an inventory system that keeps track of product stock

**Credits**<br>
Matt Fryer - https://github.com/MattFryer/HA-Mealie-Barcode-Scanner<br>
Smarthome yourself  - https://github.com/SmartHome-yourself/barcode-scanner-for-esphome<br>
