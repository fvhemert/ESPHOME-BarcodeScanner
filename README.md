# ESPHOME-BarcodeScanner


**Highlevel overview**
The illustration below depicts the highlevel implementation of the Barcode Scanner implmentation
![image](https://github.com/user-attachments/assets/8a0614ef-5037-4cc1-a282-e38d920bc123)

# Components:

**_GM67 Barcode scanner module_**<br>
<img src="https://github.com/user-attachments/assets/bf544f15-44c3-4601-a529-7d63a8ee7e7b" width=20% height=20%><br>
Barcode scanner module that supports a wide variety of barcode types and can be connceted to an ESP device through a TTL serial connection.
The device can be aquired through Amazon or Aliexpress, proct information can be found at http://www.growbarcode.com/productinfo/888736.html 
Note: I have also used the GROW GM60 stainless steel scanner module and found the module to be less reliable in low light conditions. This module does have a collimation light and also does not come with a helper (white) led.

**_Thermal printer_**<br>
<img src="https://github.com/user-attachments/assets/f2c6cd00-05cf-4cc0-b8a0-5b0033673be7" width=20% height=20%><br>
This low cost thermal printer is used to printout the shopping list. It is connected to the ESP device through the TTL serial connection, and relies on Home Assistant for output formatting. 
The printer supports both a TTL and USB serial connection but only the TTL output is used.
The printer can be ordered through Aliexpress: https://www.aliexpress.com/item/1005005604950041.html?spm=a2g0o.order_list.order_list_main.16.385c1802P4u6IT

**_SSD1306 OLED display_**

<img src="https://github.com/user-attachments/assets/21e827e9-b19b-46eb-a03a-e741e661d05c" width=20% height=20%>

A small, low cost, I2C Display 128 x 64 Pixel 0,96 inch display with white pixels. The display shows the operating mode of the scanner and the information of the product scanned.
The display an be ordered from Amzon, Aliexpress and other sources, make sure you select the I2C version, there is also a version that supports SPI.

**_Buttons_**
Simple push button switch that inludes a led. The swiutches are used to manually turn of the barcode scanner and trigger the printout of the sshopping list.

**_ESP8266_**

<img src="https://github.com/user-attachments/assets/ce8d8eb2-6eae-4f33-b7c5-548f9fe575a6" width=20% height=20%>

**_Home Assistant_**

**_Voice Assistant_**

**_Mobile Phone_**

**_Touchscreen_**


**ToDo**
- Establish 2-way communication with the printer to enable "paper out" messages.

**Credits**<br>
Matt Fryer - https://github.com/MattFryer/HA-Mealie-Barcode-Scanner<br>
https://github.com/SmartHome-yourself/barcode-scanner-for-esphome<br>
