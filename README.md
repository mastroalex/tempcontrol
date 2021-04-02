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
      - [Add extra sensor data](#add-extra-sensor-data)
      - [DS18B20](#ds18b20)
  * [ESP 32 web server and sensors reading](#esp-32-web-server-and-sensors-reading)
  * [Alexa integrations](#alexa-integrations)
    + [Sinric Pro Temperature Sensor](#sinric-pro-temperature-sensor)
      - [ESP8266](#esp8266)
      - [Alexa](#alexa)
      - [App](#app)
  * [Raspberry Pi LAMP server](#raspberry-pi-lamp-server)
- [Future implementations](#future-implementations)
- [References](#references)

### Shopping bag
* DHT11 / DHT22 / DHT12
* ESP32
* ESP8266
* Raspberry Pi
* DS18B20
* 4.7k Ohm Resistor 

# Why 

## Temperature and humidity 

Thermal comfort is the condition of mind that expresses satisfaction with the thermal environment. The human body can be viewed as a heat engine where food is the input energy. The human body will release excess heat into the environment and the heat transfer is proportional to temperature difference. 
Both the hot and cold scenarios lead to discomfort. 


Sever study show how negative health and well-being outcomes associated with increasing frequency of heat stress interfering with daily activities. 

The main factors that influence thermal comfort are those that determine heat gain and loss, namely metabolic rate, clothing insulation, air temperature, mean radiant temperature, air speed and relative humidity. Psychological parameters, such as individual expectations, also affect thermal comfort. The thermal comfort temperature may vary greatly between individuals and depending on factors such as activity level, clothing, and humidity.

<img src="https://upload.wikimedia.org/wikipedia/commons/f/f9/Psychrometric_chart_-_PMV_method.png" alt="psychrometric" width="500"> 

> From [Wikipedia](https://en.wikipedia.org/wiki/Thermal_comfort#/media/File:Psychrometric_chart_-_PMV_method.png) - This psychrometric chart represents the acceptable combination of air temperature and humidity values, according to the PMV/PPD method in the ASHRAE 55-2010 Standard. The comfort zone in blue represents the 90% of acceptability,



Generally, humans do not perform well under thermal stress with performances lower than their performance at normal thermal wet conditions.  Some of the physiological effects of thermal heat stress include increased blood flow to the skin, sweating, and increased ventilation.

## Luminous well-being

People receive about 85 percent of their information through their sense of sight. Appropriate lighting, without glare or shadows, can reduce eye fatigue and headaches; it can prevent workplace incidents by increasing the visibility of moving machinery and other safety hazards. Good quality lighting also reduces the chance of incidents and injuries from "momentary blindness" (momentary low field vision due to eyes adjusting from brighter to darker, or vice-versa, surroundings).

The ability to "see" at work depends not only on lighting but also on:

- The time to focus on an object. Fast moving objects are hard to see.
- The size of an object. Very small objects are hard to see.
- Brightness. Too much or too little reflected light makes objects hard to see.
- Contrast between an object and its immediate background. Too little contrast makes it hard to distinguish an object from the background.

<img src="https://www.smow.com/pics/g/w/2064/smow-planungsthemen-licht-arbeitsplatz-grafik-lichteinfall-2.jpg" alt="system" width="500"/>

Light sources can be divided between natural light and electrical sources. In the case of electric light sources, different types of light bulbs can produce different effects. The effects range from the warmth of light to energy efficiency to the effect on eyesight to the colour rendering.

Not enough light can also be a problem so even in workplaces where daylight is available, it is essential to have a good electric lighting system.

There are three basic types of lighting:
- General.
- Localized-general
- Local (or task).

<img src="https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.ilepower.com%2Fblogs%2Fblog%2Fthe-three-basic-types-of-lighting&psig=AOvVaw1B3Ts5OYLMU8_2-pl3wGG-&ust=1617485092275000&source=images&cd=vfe&ved=0CAIQjRxqFwoTCIDQ_9S_4O8CFQAAAAAdAAAAABAN" alt="system" width="600"/> 

Eye discomfort is a general term which can include some or many symptoms. It may be part of “computer vision syndrome” which includes: eyestrain, dry eyes, blurred vision, red or pink eyes, burning, light sensitivity, headaches, and pain in the shoulders, neck and back.
Eye discomfort symptoms may be caused by: poor lighting, glare on a computer or tablet screen, poor quality computer or tablet screen (e.g., poor resolution, blurry image, etc.), improper viewing distances, poor seating posture, uncorrected vision problems, dry air, air movement, or a combination of these factors.

The recommended range of illuminations varies in different task. From simple orientation 100-500 Lux (lumens for square inches), to visual tasks of medium contrast or small size 700-1200 Lux, to visual tasks of low contrast and very small size over a prolonged period 2000-5000 Lux.

It is also important to valuate reflectance of object, glare and reflectance.


## Air quality 

Indoor air quality (IAQ) problems result from interactions between building materials and furnishing, activities within the building, climate, and building occupants. IAQ problems may arise from one or more of the following causes:

- Indoor environment - inadequate temperature, humidity, poor air circulation, ventilation system issues.
- Indoor air contaminants - chemicals, dusts, moulds or fungi, bacteria, gases, vapours, odours.
- Insufficient outdoor air intake.

<img src="https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.kaggle.com%2Fritesh7355%2Fair-quality-indexnew-delhi&psig=AOvVaw0LVEL4zv3IS5_5qZP6lwt-&ust=1617485178940000&source=images&cd=vfe&ved=0CAIQjRxqFwoTCMCcp_6_4O8CFQAAAAAdAAAAABAJ" alt="system" width="500"/>

## Acoustic well-being
<img src="https://images.adsttc.com/media/images/5ec1/84fe/b357/6510/6b00/09d2/newsletter/Graph_final-01.jpg?1589740784" alt="system" width="500"/>

# Project 

The project is divided into several phases.
After the fist stage of sensor testing and related library studies the idea is to use a local web server (on the ESP32) to read the data from LAN. This will also help analyze the ideal position to put the sensory box. The web server is an asynchronous web server built using the [ESPAsyncWebServer library](https://github.com/me-no-dev/ESPAsyncWebServer) so the sensor reading is update automatically without the need to refresh the web page. 

The third phase consists to allowing alexa to read temperature with [Sinric](https://sinric.com/login?returnUrl=%2F). This will also allow us to control EPS32  outputs through Alexa vocal commands.

Sinric permit a fists remote reading and logging for sensors data but only for temperature, humidity, air quality and energy consumption. 

The most important step is to create a LAMP web server on Raspberry Pi and to use it to publish data. This also allows us to integrate several EPS32s (e.g. for different rooms) into a single web server.

In the following image the block diagram for final configurations:

<img src="https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2019/09/Raspberry-Pi-LAMP-Server-ESP32-ESP8266-Overview.png?quality=100&strip=all&ssl=1" alt="system" width="700"/>

> Image credits [Ed - randomnerdtutorials.com](https://randomnerdtutorials.com/esp32-esp8266-raspberry-pi-lamp-server/)

To avoid overloading the LAN network, a radio communication system between raspberry and ESP32 could be developed. Or a radio link could be created between two ESP32s (or any other board similar to Arduino).

Finally all the sensors and the relative esp32 will be soldered on a board and the project will be enclosed in a 3d printed case. It is not excluded that the project will also be transferred to a customized pcb.

A last possibility is to open the firewall ports and allow access to the webserver remotely.

Another options is to have a domain name and hosting account that allows to store sensor readings from the ESP32 or ESP8266. It's possible to visualize the readings from anywhere in the world by accessing your own server domain.

<img src="https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2019/08/ESP32-MySQL-Charts-Project-Overview.png?w=750&quality=100&strip=all&ssl=1" alt="system" width="700"/>

##  ESP 8622 web server and sensors reading

>  **For general sensor reading search in** [example folder](https://github.com/mastroalex/tempcontrol/blob/main/esp8266_sensor_reading/README.md) 

In this case a wemos D32 is used. 
To use it with Arduino IDE instal ESP8266 Library and select NodeMCU 1 (ESP-12E) board. It is also important to install CH340G driver.
Use this link [esp8266 package](http://arduino.esp8266.com/stable/package_esp8266com_index.json) to add board manager and after install esp8266.

<img src="https://i2.wp.com/randomnerdtutorials.com/wp-content/uploads/2019/05/ESP8266-NodeMCU-kit-12-E-pinout-gpio-pin.png?w=817&quality=100&strip=all&ssl=1" alt="nodemcu12E" width="500"/>

---

**Circuit diagram**

<img src="https://github.com/mastroalex/tempcontrol/blob/main/esp8266_sensor_reading/sensor_esp8266_bb.png" alt="nodemcu12E" width="900"/>

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

> **For other info** [DHT11 Reading Example](https://github.com/mastroalex/tempcontrol/tree/main/esp8266_sensor_reading/dht11_test) 

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

The example code is presented in [esp8266 example folder](https://github.com/mastroalex/tempcontrol/tree/main/esp8266webserveinfo)

Insert wifi credentials!
```c
const char* ssid = "REPLACE_WITH_YOUR_SSID";
const char* password = "REPLACE_WITH_YOUR_PASSWORD";
```

The webpage include heading and differents paragraph to display sensor data.There are also two icons to style the page.

All the HTML text with styles included is stored in the index_html variable. Now we’ll go through the HTML text and see what each part does.

```html
<p>
  <i class="fas fa-thermometer-half" style="color:#059e8a;"</i> 
  <span class="dht-labels">Temperature</span> 
  <span id="temperature">%TEMPERATURE%</span>
  <sup class="units">°C</sup>
</p>
```

For automatic updates ther's some JavaScript code in our web page that updates the temperature and humidity automatically, every 10 seconds. Scripts in HTML text should go between the `<script></script>` tags.

Example for temperature:
```js
<script>
setInterval(function ( ) {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
      document.getElementById("temperature").innerHTML = this.responseText;
    }
  };
  xhttp.open("GET", "/temperature", true);
  xhttp.send();
}, 10000 ) ;
``` 

To update data on the background, there is a setInterval() function that runs every 10 seconds.
Basically, it makes a request in the `/data` URL to get the latest `data`:
```js
  xhttp.open("GET", "/data", true);
  xhttp.send();
}, 10000 ) ;
```
When it receives that value, it updates the HTML element whose id is data.
```js
if (this.readyState == 4 && this.status == 200) {
  document.getElementById("temperature").innerHTML = this.responseText;
}
```
<img src="https://github.com/mastroalex/tempcontrol/blob/main/esp8266webserveinfo/webserver.png" alt="system" width="700"/>

Another important function is `processor()` function, that will replace the placeholders in our HTML text with the actual temperature and humidity values.
```c
String processor(const String& var){
  //Serial.println(var);
  if(var == "TEMPERATURE"){
    return String(t);
  }
  else if(var == "DATA"){
    return String(data);
  }
  return String();
}
```
When the web page is requested, we check if the HTML has any placeholders. If it finds the `%DATA%` placeholder, we return the temperature that is stored on the data variable.

In `setup()` there are different initializations. 
Connection to your local network and print the ESP8266 IP address:
```c
WiFi.begin(ssid, password);
while (WiFi.status() != WL_CONNECTED) {
  delay(1000);
  Serial.println("Connecting to WiFi..");
}
```

And the code to handle the web server.
When we make a request on the root URL, we send the HTML text that is stored on the `index_html` variable. We also need to pass the `processor` function, that will replace all the placeholders with the right values.
```c
server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
  request->send_P(200, "text/html", index_html, processor);
});
```

Add additional handlers to update the temperature and humidity readings. When we receive a request on the `/data` URL, we simply need to send the updated temperature value. It is plain text, and it should be sent as a char, so, we use the `c_str()` method.

```c
server.on("/data", HTTP_GET, [](AsyncWebServerRequest *request){
  request->send_P(200, "text/plain", String(data).c_str());
});
```

Start webserver
```c
server.begin();
```

It is recommended to print the sensor data on the serial monitor through the `loop()` for a debug function.

___

#### Add extra sensor data


Create variabile for data reading and update it in `loop()`

```c
float data = 0;
```
Ad paragraph and placeholder into HTML section:
```html
 <p>
    <i class="fas fa-temperature-high" style="color:#b32d00;"></i> 
    <span class="dht-labels">Data</span> 
    <span id="dataid">%DATA%</span>
    <sup class="units">&deg;C</sup>
  </p>
```

Ad section into `<script>` block for the placeholder:
```js
setInterval(function ( ) {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
      document.getElementById("dataid").innerHTML = this.responseText;
    }
  };
  xhttp.open("GET", "/data", true);
  xhttp.send();
}, 10000) ;
```

Ad `else if` section into processor():
```c
else if (var == "DATA") { // DHT humidity
    return String(data);
  }
```

Another options is to obtain sensor data with a specially created function and update it in this last section taking its data as a string. Suppose this function called `specialfunction()` this last paragraph would be:

```c
else if (var == "DATA") { // DHT humidity
    return String(specialfunction());
  }
```
___

> **For other info to** [ESP8266 Web Server Extra](https://github.com/mastroalex/tempcontrol/tree/main/esp8266webserveinfo)

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

> **For other info** [DS18B20 Reading Exampe](https://github.com/mastroalex/tempcontrol/tree/main/esp8266_sensor_reading/ds18b20_test)

---

##  ESP 32 web server and sensors reading

<img src="https://i2.wp.com/randomnerdtutorials.com/wp-content/uploads/2018/08/ESP32-DOIT-DEVKIT-V1-Board-Pinout-36-GPIOs-updated.jpg?w=750&quality=100&strip=all&ssl=1" alt="esp32" width="500"/>

The section for ESP 32 will be updated soon.

---


## Alexa integrations 

For integration with Alexa we will use the functions offered by Sinric Pro.

---
### Sinric Pro Temperature Sensor

Sinric pro is a very practical platform that allows us to connect the development board to Alexa and Google Home.
The first step is to create an account on [Sinric Pro](https://portal.sinric.pro/register).

#### ESP8266

For the wifi connection, DHT reading and calbe connection we use the same web server procedure indicated above.

Install and include library and dependencies for SinricPro:
```c
#include <SinricPro.h>
#include "SinricProTemperaturesensor.h"
```

Define the following constants available from the web page of your Sinric account:

```c
#define APP_KEY           "YOUR-APP-KEY"      
#define APP_SECRET        "YOUR-APP-SECRET"   
#define TEMP_SENSOR_ID    "YOUR-DEVICE-ID"   
bool deviceIsOn;                              // Temeprature sensor on/off state
float lastTemperature;                        // last known temperature (for compare)
float lastHumidity;                           // last known humidity (for compare)
#define EVENT_WAIT_TIME   60000               // send event every 60 seconds
unsigned long lastEvent = (-EVENT_WAIT_TIME); // last time event has been sent 
```
There are other important function like `onPowerState()` that callback for setPowerState request parameters and return true if request should be marked as handled correctly / false if not. The `handleTemperatatureSensor()` that checks if `Temperaturesensor` is turned on, get actual temperature and humidity and check if these values are valid and send event to SinricPro Server if temperature or humidity changed. The `setupSinricPro()` to inizialize Sinric comunciation.

So they look like:

```c

bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Temperaturesensor turned %s (via SinricPro) \r\n", state ? "on" : "off");
  deviceIsOn = state; // turn on / off temperature sensor
  return true; // request handled properly
}

void handleTemperaturesensor() {
  if (deviceIsOn == false) return; // device is off...do nothing

  unsigned long actualMillis = millis();
  if (actualMillis - lastEvent < EVENT_WAIT_TIME) return; //only check every EVENT_WAIT_TIME milliseconds

  t = dht.readTemperature();          // get actual temperature in °C
  //  temperature = dht.getTemperature() * 1.8f + 32;  // get actual temperature in °F
  h = dht.readHumidity();                // get actual humidity

  if (isnan(t) || isnan(h)) { // reading failed...
    Serial.printf("DHT reading failed!\r\n");  // print error message
    return;                                    // try again next time
  }

  if (t == lastTemperature || h == lastHumidity) return; // if no values changed do nothing...

  SinricProTemperaturesensor &mySensor = SinricPro[TEMP_SENSOR_ID];  // get temperaturesensor device

  bool success = mySensor.sendTemperatureEvent(t, h); // send event
  if (success) {  // if event was sent successfuly, print temperature and humidity to serial
    Serial.printf("Temperature: %2.1f Celsius\tHumidity: %2.1f%%\r\n", t, h);
  } else {  // if sending event failed, print error message
    Serial.printf("Something went wrong...could not send Event to server!\r\n");
  }

  lastTemperature = t;  // save actual temperature for next compare
  lastHumidity = h;        // save actual humidity for next compare
  lastEvent = actualMillis;       // save actual time for next compare
}


// setup function for SinricPro
void setupSinricPro() {
  // add device to SinricPro
  SinricProTemperaturesensor &mySensor = SinricPro[TEMP_SENSOR_ID];
  mySensor.onPowerState(onPowerState);

  // setup SinricPro
  SinricPro.onConnected([]() {
    Serial.printf("Connected to SinricPro\r\n");
  });
  SinricPro.onDisconnected([]() {
    Serial.printf("Disconnected from SinricPro\r\n");
  });
  SinricPro.begin(APP_KEY, APP_SECRET);
  SinricPro.restoreDeviceStates(true); // get latest known deviceState from server (is device turned on?)
}

```

Include in `setup()` the `setupSinricPro()` function.

Call in `loop()` the function:

```c
  SinricPro.handle();
  handleTemperaturesensor(); 
```
#### Alexa

It's possibile to add Sinric Pro skills in Alexa app and login with Sinric credential to add device.

<img src="https://github.com/mastroalex/tempcontrol/blob/main/esp8266sinric/alexa_dht_mobileapp.png" alt="alexa_app_dht" width="400">

#### App 

It's possibile to download the Sinric Pro App or to login into SinricPro website to visualize sensor data log for different time period. 

<img src="https://github.com/mastroalex/tempcontrol/blob/main/esp8266sinric/sinric_dht_mobileapp.png" alt="sinric_app_dht" width="400">




> **Other information and complete sketch in** [esp8266 sinric example folder](https://github.com/mastroalex/tempcontrol/tree/main/esp8266sinric)


This code will be integrated with that of the web server.

---

## Raspberry Pi LAMP server

**The following has been taken from [ESP32/ESP8266 Publish Data to Raspberry Pi LAMP Server](https://randomnerdtutorials.com/esp32-esp8266-raspberry-pi-lamp-server/) and not yet revised**

--- 



## Private domanin 


**The following has been taken from [Visualize Your Sensor Readings from Anywhere in the World](https://randomnerdtutorials.com/visualize-esp32-esp8266-sensor-readings-from-anywhere/) and not yet revised**

--- 





# Future implementations

# References 
- [ESP32/ESP8266 Publish Data to Raspberry Pi LAMP Server](https://randomnerdtutorials.com/esp32-esp8266-raspberry-pi-lamp-server/)
- [ALEXA ECHO comunica con ESP32 and ESP8266 utilizzando SINRIC](https://www.youtube.com/watch?app=desktop&v=nYen86CvUzg&t=871s)
- [SinricPro (ESP8266 / ESP32 SDK)](https://github.com/sinricpro/esp8266-esp32-sdk)
- [ESP32 DHT11/DHT22 Web Server](https://randomnerdtutorials.com/esp32-dht11-dht22-temperature-humidity-web-server-arduino-ide/)
- [Alexa comunica con ESP32 and ESP8266 utilizzando SINRIC con sonoff ](https://www.ingeimaks.it/alexa-comunica-con-ESP32-and-ESP8266-utilizzando-SINRIC-GUIDA-ITALIANO/index.html)
- [DIY Cloud Weather Station with ESP32/ESP8266](https://randomnerdtutorials.com/cloud-weather-station-esp32-esp8266/)
- [Control ESP32 and ESP8266 GPIOs from Anywhere in the World](https://randomnerdtutorials.com/control-esp32-esp8266-gpios-from-anywhere/)
- [ESP8266 DHT11/DHT22 Temperature and Humidity Web Server with Arduino IDE](https://randomnerdtutorials.com/esp8266-dht11dht22-temperature-and-humidity-web-server-with-arduino-ide/)
- [Sinric.com]()
- [SinricPro (ESP8266 / ESP32 SDK)](https://github.com/sinricpro/esp8266-esp32-sdk)
- [Sinric Example for ESP](https://github.com/sinricpro/esp8266-esp32-sdk/tree/master/examples)
- [DIY Air Quality Monitor](https://howtomechatronics.com/projects/diy-air-quality-monitor-pm2-5-co2-voc-ozone-temp-hum-arduino-meter/)
- [Visualize Your Sensor Readings from Anywhere in the World](https://randomnerdtutorials.com/visualize-esp32-esp8266-sensor-readings-from-anywhere/)
- [Thermal comfort](https://en.wikipedia.org/wiki/Thermal_comfort)
- [A Meta-Analysis of Performance Response Under Thermal Stressors](https://journals.sagepub.com/doi/10.1518/001872007X230226)
- [Thermoregulatory responses to environmental toxicants](https://www.sciencedirect.com/science/article/abs/pii/S0041008X08000288?via%3Dihub)
- [The Importance of Humidity in the Relationship between Heat and Population Mental Health](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0164190)