# Environmental conditions measurement unit

The purpose of this project is to create a station for monitoring the environmental conditions for home. The device will be open source and based on Arduino and NodeMCU.

### Table of content
- [Environmental conditions measurement unit](#environmental-conditions-measurement-unit)
    + [Table of content](#table-of-content)
    + [Shopping bag](#shopping-bag)
- [Why](#why)
  * [Temperature and humidity](#temperature-and-humidity)
  * [Luminous well-being](#luminous-well-being)
  * [Acoustic well-being](#acoustic-well-being)
- [Project](#project)
  * [ESP 8622 web server and sensors reading](#esp-8622-web-server-and-sensors-reading)
      - [DHT](#dht)
      - [Web server](#web-server)
      - [DS18B20](#ds18b20)
  * [ESP 32 web server and sensors reading](#esp-32-web-server-and-sensors-reading)
  * [Alexa integrations](#alexa-integrations)
  * [Raspberry Pi web server](#raspberry-pi-web-server)
- [Future implementations](#future-implementations)
- [References](#references)

### Shopping bag
* DHT11 / DHT22
* ESP32
* ESP8266
* Raspberry PI
* DS18B20
* 4.7k Ohm Resistor 

# Why 

## Temperature and humidity 
<img src="https://www.cvbeltrame.it/wp-content/uploads/2014/06/Comfort-3.jpg" alt="system" width="500"/>


## Luminous well-being
<img src="https://www.smow.com/pics/g/w/2064/smow-planungsthemen-licht-arbeitsplatz-grafik-lichteinfall-2.jpg" alt="system" width="500"/>

## Acoustic well-being
<img src="https://images.adsttc.com/media/images/5ec1/84fe/b357/6510/6b00/09d2/newsletter/Graph_final-01.jpg?1589740784" alt="system" width="500"/>

# Project 

The project is divided into several phases.
After the fist stage of sensor testing and related library studies the idea is to use a local web server (on the ESP32) to read the data from LAN. This will also help analyze the ideal position to put the sensory box. The web server is an asynchronous web server built using the [ESPAsyncWebServer library](https://github.com/me-no-dev/ESPAsyncWebServer) so the sensor reading is update automatically without the need to refresh the web page. 

The third phase consists to allowing alexa to read temperature with [Sinric](https://sinric.com/login?returnUrl=%2F). This will also allow us to control EPS32  outputs through Alexa vocal commands.

The most important step is to create a LAMP web server on Raspberry Pi and to use it to publish data. This also allows us to integrate several EPS32s (e.g. for different rooms) into a single web server.

In the following image the block diagram for final configurations:

<img src="https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2019/09/Raspberry-Pi-LAMP-Server-ESP32-ESP8266-Overview.png?quality=100&strip=all&ssl=1" alt="system" width="600"/>

> Image credits [Ed - randomnerdtutorials.com](https://randomnerdtutorials.com/esp32-esp8266-raspberry-pi-lamp-server/)

To avoid overloading the LAN network, a radio communication system between raspberry and ESP32 could be developed. Or a radio link could be created between two ESP32s (or any other board similar to Arduino).

Finally all the sensors and the relative esp32 will be soldered on a board and the project will be enclosed in a 3d printed case. It is not excluded that the project will also be transferred to a customized pcb.

A last possibility is to open the firewall ports and allow access to the webserver remotely.


##  ESP 8622 web server and sensors reading

>  For general sensor reading search in [example folder](https://github.com/mastroalex/tempcontrol/blob/main/esp8266_sensor_reading/README.md) 

In this case a wemos D32 is used. 
To use it with Arduino IDE instal ESP8266 Library and select NodeMCU 1 (ESP-12E) board. It is also important to install CH340G driver.
Use this link [esp8266 package](http://arduino.esp8266.com/stable/package_esp8266com_index.json) to add board manager and after install esp8266.

<img src="https://i2.wp.com/randomnerdtutorials.com/wp-content/uploads/2019/05/ESP8266-NodeMCU-kit-12-E-pinout-gpio-pin.png?w=817&quality=100&strip=all&ssl=1" alt="nodemcu12E" width="400"/>

---

#### DHT 
To read DHT11 is used DTH library from Adafruit. 
Frist include library and define pin
 ```c
#include "DHT.h"
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);
// Reading temperature or humidity takes about 250 milliseconds!
// Sensor readings may also be up to 2 seconds 'old' (its a very slow sensor)
float h = dht.readHumidity();
// Read temperature as Celsius (the default)
float t = dht.readTemperature();
  ```
Define variables and print it with:
 ```c
Serial.print(h)
Serial.print(t)
 ```

> ## For other info [DHT11 Reading Example](https://github.com/mastroalex/tempcontrol/tree/main/esp8266_sensor_reading/dht11_test) 

---

#### Web server
The web server can be accessed with any device that has a browser on your local network.
This is an asynchronous web server that update data automatically without need to refresh the web page and with custom CSS to style the web page.

**Complete description** in: [ESP8266 Web Server Extra](https://github.com/mastroalex/tempcontrol/tree/main/esp8266webserveinfo)


<img src="https://github.com/mastroalex/tempcontrol/blob/main/esp8266webserveinfo/webserver_example.png" alt="system" width="300"/>

> DS18B20 hand held to see temperature difference

Install the DHT library by Adafruit.

Follow the next step to install the ESPAsyncWebServer library:
1. Download [AsyncWebServer library](https://github.com/me-no-dev/ESPAsyncWebServer/archive/master.zip)
2. Unzip the .zip folder
3. Rename your folder from ~~ESPAsyncWebServer-master~~ to ESPAsyncWebServer
4. Move the ESPAsyncWebServer folder to your Arduino IDE installation libraries folder

Follow the next step to install the ESPAsync TCP Library:
1. Download [ESPAsync TCP library](https://github.com/me-no-dev/ESPAsyncWebServer/archive/master.zip)
2. Unzip
3. Rename your folder from ~~ESPAsyncTCP-master~~ to ESPAsyncTCP
4. Move the ESPAsyncTCP folder to your Arduino IDE installation libraries folder
Restard Arduino IDE.

The example code is presented in [esp8266 example folder](https://github.com/mastroalex/tempcontrol/tree/main/esp8266_sensor_readiing)

Insert wifi credentials!
```c
const char* ssid = "REPLACE_WITH_YOUR_SSID";
const char* password = "REPLACE_WITH_YOUR_PASSWORD";
```

> ## For other info to [ESP8266 Web Server Extra](https://github.com/mastroalex/tempcontrol/tree/main/esp8266webserveinfo)

> Thanks to Rui Santos. For other information and code comments [DHT11 ESP8266 Web Server](https://randomnerdtutorials.com/esp8266-dht11dht22-temperature-and-humidity-web-server-with-arduino-ide/)

---

#### DS18B20

Install library for DS18B20: install [OneWire](https://github.com/PaulStoffregen/OneWire) from library manager. Install Dallas Temperature library by Miles Burton from library manager.

Include library and define pin:
```c
#include <OneWire.h>
#include <DallasTemperature.h>
// GPIO where the DS18B20 is connected to
const int oneWireBus = D5;     
```
Setup comincation:
```c
OneWire oneWire(oneWireBus);
DallasTemperature sensors(&oneWire);
```

In the `setup()` section start sensors and serial.
Request temperature with:
```c
sensors.requestTemperatures(); 
float temperatureC = sensors.getTempCByIndex(0);
```

> ## For other info [DS18B20 Reading Exampe](https://github.com/mastroalex/tempcontrol/tree/main/esp8266_sensor_reading/ds18b20_test)

---

##  ESP 32 web server and sensors reading

<img src="https://i2.wp.com/randomnerdtutorials.com/wp-content/uploads/2018/08/ESP32-DOIT-DEVKIT-V1-Board-Pinout-36-GPIOs-updated.jpg?w=750&quality=100&strip=all&ssl=1" alt="esp32" width="400"/>

The section for ESP 32 will be updated soon.

---


## Alexa integrations 

For integration with Alexa we will use the functions offered by Sinric Pro.

---

## Raspberry Pi web server


# Future implementations

# References 
- [ESP32/ESP8266 Publish Data to Raspberry Pi LAMP Server](https://randomnerdtutorials.com/esp32-esp8266-raspberry-pi-lamp-server/)
- [ALEXA ECHO comunica con ESP32 and ESP8266 utilizzando SINRIC](https://www.youtube.com/watch?app=desktop&v=nYen86CvUzg&t=871s)
- [ESP32 DHT11/DHT22 Web Server](https://randomnerdtutorials.com/esp32-dht11-dht22-temperature-humidity-web-server-arduino-ide/)
- [DIY Cloud Weather Station with ESP32/ESP8266](https://randomnerdtutorials.com/cloud-weather-station-esp32-esp8266/)
- [Control ESP32 and ESP8266 GPIOs from Anywhere in the World](https://randomnerdtutorials.com/control-esp32-esp8266-gpios-from-anywhere/)
-[ESP8266 DHT11/DHT22 Temperature and Humidity Web Server with Arduino IDE](https://randomnerdtutorials.com/esp8266-dht11dht22-temperature-and-humidity-web-server-with-arduino-ide/)

