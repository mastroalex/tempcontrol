# Environmental conditions measurement unit


The purpose of this project is to create a station for monitoring the environmental conditions for home. The device will be open source and based on Arduino and NodeMCU.

 
### Table of content
- [Environmental conditions measurement unit](#environmental-conditions-measurement-unit)
    + [Shopping bag](#shopping-bag)
- [Why](#why)
  * [Temperature and humidity](#temperature-and-humidity)
  * [Luminous well-being](#luminous-well-being)
  * [Air quality](#air-quality)
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
  * [Raspberry Pi LAMP server](#raspberry-pi-lamp-server)
    + [Preparing MySQL Database](#preparing-mysql-database)
    + [PHP Script HTTP POST – Insert Data in MySQL Database](#php-script-http-post---insert-data-in-mysql-database)
    + [PHP Script – Display Database Content](#php-script---display-database-content)
    + [Preparing Your ESP32 or ESP8266](#preparing-your-esp32-or-esp8266)
  * [Private domain server](#private-domain-server)
    + [Hosting Your PHP Application and MySQL Database](#hosting-your-php-application-and-mysql-database)
    + [Preparing Your ESP32 or ESP8266](#preparing-your-esp32-or-esp8266-1)
    + [Code complete](#code-complete)
    + [Personalize it](#personalize-it)
  * [Tasmota Smart Control](#tasmota-smart-control)
- [Future implementations](#future-implementations)
- [Other project](#other-project)
- [Contributors](#contributors)
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

<img src="https://upload.wikimedia.org/wikipedia/commons/f/f9/Psychrometric_chart_-_PMV_method.png" alt="psychrometric" width="800"> 

> From [Wikipedia](https://en.wikipedia.org/wiki/Thermal_comfort#/media/File:Psychrometric_chart_-_PMV_method.png) - This psychrometric chart represents the acceptable combination of air temperature and humidity values, according to the PMV/PPD method in the ASHRAE 55-2010 Standard. The comfort zone in blue represents the 90% of acceptability,



Generally, humans do not perform well under thermal stress with performances lower than their performance at normal thermal wet conditions.  Some of the physiological effects of thermal heat stress include increased blood flow to the skin, sweating, and increased ventilation.

## Luminous well-being

People receive about 85 percent of their information through their sense of sight. Appropriate lighting, without glare or shadows, can reduce eye fatigue and headaches; it can prevent workplace incidents by increasing the visibility of moving machinery and other safety hazards. Good quality lighting also reduces the chance of incidents and injuries from "momentary blindness" (momentary low field vision due to eyes adjusting from brighter to darker, or vice-versa, surroundings).

The ability to "see" at work depends not only on lighting but also on:

- The time to focus on an object. Fast moving objects are hard to see.
- The size of an object. Very small objects are hard to see.
- Brightness. Too much or too little reflected light makes objects hard to see.
- Contrast between an object and its immediate background. Too little contrast makes it hard to distinguish an object from the background.

<img src="https://www.smow.com/pics/g/w/2064/smow-planungsthemen-licht-arbeitsplatz-grafik-lichteinfall-2.jpg" alt="light" width="800"/>

Light sources can be divided between natural light and electrical sources. In the case of electric light sources, different types of light bulbs can produce different effects. The effects range from the warmth of light to energy efficiency to the effect on eyesight to the colour rendering.

Not enough light can also be a problem so even in workplaces where daylight is available, it is essential to have a good electric lighting system.

There are three basic types of lighting:
- General.
- Localized-general
- Local (or task).

<img src="https://cdn.shopify.com/s/files/1/0107/2847/2676/files/3_0a4242f8-4aa5-4dbb-81a6-5a2b4b6bec30_grande.jpg?v=1551160494" alt="system" width="800"/> 

Eye discomfort is a general term which can include some or many symptoms. It may be part of “computer vision syndrome” which includes: eyestrain, dry eyes, blurred vision, red or pink eyes, burning, light sensitivity, headaches, and pain in the shoulders, neck and back.
Eye discomfort symptoms may be caused by: poor lighting, glare on a computer or tablet screen, poor quality computer or tablet screen (e.g., poor resolution, blurry image, etc.), improper viewing distances, poor seating posture, uncorrected vision problems, dry air, air movement, or a combination of these factors.

The recommended range of illuminations varies in different task. From simple orientation 100-500 Lux (lumens for square inches), to visual tasks of medium contrast or small size 700-1200 Lux, to visual tasks of low contrast and very small size over a prolonged period 2000-5000 Lux.

It is also important to valuate reflectance of object, glare and reflectance.


## Air quality 

Indoor air quality (IAQ) problems result from interactions between building materials and furnishing, activities within the building, climate, and building occupants. IAQ problems may arise from one or more of the following causes:

- Indoor environment - inadequate temperature, humidity, poor air circulation, ventilation system issues.
- Indoor air contaminants - chemicals, dusts, moulds or fungi, bacteria, gases, vapours, odours.
- Insufficient outdoor air intake.

<img src="https://www.deq.ok.gov/wp-content/uploads/air-division/aqi_mini-768x432.png" alt="system" width="800"/>

## Acoustic well-being
<img src="https://images.adsttc.com/media/images/5ec1/84fe/b357/6510/6b00/09d2/newsletter/Graph_final-01.jpg?1589740784" alt="system" width="800"/>

# Project 

The project is divided into several phases.
After the fist stage of sensor testing and related library studies the idea is to use a local web server (on the ESP32) to read the data from LAN. This will also help analyze the ideal position to put the sensory box. The web server is an asynchronous web server built using the [ESPAsyncWebServer library](https://github.com/me-no-dev/ESPAsyncWebServer) so the sensor reading is update automatically without the need to refresh the web page. 

The third phase consists to allowing alexa to read temperature with [Sinric](https://sinric.com/login?returnUrl=%2F). This will also allow us to control EPS32  outputs through Alexa vocal commands.

**Sinric permit a fists remote reading and logging for sensors data but only for temperature, humidity, air quality and energy consumption.**

<img src="https://github.com/mastroalex/tempcontrol/blob/main/esp8266sinric/sinric_diagram.png" alt="sinriclogdiagram" width="1000"/>

The most important step is to create a LAMP web server on Raspberry Pi and to use it to publish data. This also allows us to integrate several EPS32s (e.g. for different rooms) into a single web server.

In the following image the block diagram for final configurations:

<img src="https://github.com/mastroalex/tempcontrol/blob/main/diagram/lamp_raspberry.jpg" alt="system" width="1000"/>


To avoid overloading the LAN network, a radio communication system between raspberry and ESP32 could be developed. Or a radio link could be created between two ESP32s (or any other board similar to Arduino).

Finally all the sensors and the relative esp32 will be soldered on a board and the project will be enclosed in a 3d printed case. It is not excluded that the project will also be transferred to a customized pcb.

A last possibility is to open the firewall ports and allow access to the webserver remotely.

Another options is to have a domain name and hosting account that allows to store sensor readings from the ESP32 or ESP8266. It's possible to visualize the readings from anywhere in the world by accessing your own server domain.

<img src="https://github.com/mastroalex/tempcontrol/blob/main/diagram/web_server.jpg" alt="system" width="1000"/>

##  ESP 8622 web server and sensors reading

>  **For general sensor reading search in** [example folder](https://github.com/mastroalex/tempcontrol/blob/main/esp8266_sensor_reading/README.md) 

In this case a wemos D32 is used. 
To use it with Arduino IDE instal ESP8266 Library and select NodeMCU 1 (ESP-12E) board. It is also important to install CH340G driver.
Use this link [esp8266 package](http://arduino.esp8266.com/stable/package_esp8266com_index.json) to add board manager and after install esp8266.

<img src="https://i2.wp.com/randomnerdtutorials.com/wp-content/uploads/2019/05/ESP8266-NodeMCU-kit-12-E-pinout-gpio-pin.png?w=817&quality=100&strip=all&ssl=1" alt="nodemcu12E" width="800"/>

---

**Circuit diagram**

<img src="https://github.com/mastroalex/tempcontrol/blob/main/esp8266_sensor_reading/sensor_esp8266_bb.png" alt="nodemcu12E" width="800"/>

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

<img src="https://i2.wp.com/randomnerdtutorials.com/wp-content/uploads/2018/08/ESP32-DOIT-DEVKIT-V1-Board-Pinout-36-GPIOs-updated.jpg?w=750&quality=100&strip=all&ssl=1" alt="esp32" width="700"/>

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

<img src="https://rhea.co.in/wp-content/uploads/2019/11/LAMP-stack.jpg" alt="rasp_lamp" width="800">

> **This section has not been detailed see [private domain server](#private-domain-server) for better content.**

### Preparing MySQL Database

After installing a LAMP server and phpMyAdmin on Raspberry Pi, you can login into phpMyAdmin. After that, follow the next steps to create your database and SQL table.

Open your browser and type `http://Your-Raspberry-Pi-IP-Address/phpmyadmin`, your should see the login page for phpMyAdmin web interface.

**Create Database**

Select the “Databases” menu at the top, complete the “Create database” fields:
- esp_data
- utf8mb4_general_ci

Then, press the Create button:

That’s it! Your new database was created successfully. Now, save your database name because you’ll need it later:

- Database name: `esp_data`

**Creating a SQL table**

After creating your database, in the left sidebar select your database name `esp_data`:

Raspberry Pi phpMyAdmin open new database

> Important: make sure you’ve opened the `esp_data` database. Then, click the SQL tab. If you don’t follow these exact steps and run the SQL query, you might create a table in the wrong database.

Copy the SQL query in the following snippet:

```sql
CREATE TABLE SensorData (
    id INT(6) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    sensor VARCHAR(30) NOT NULL,
    location VARCHAR(30) NOT NULL,
    value1 VARCHAR(10),
    value2 VARCHAR(10),
    value3 VARCHAR(10),
    reading_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
)
```

Open the “SQL” tab, paste it in the SQL query field (highlighted with a red rectangle) and press the Go button to create your table:

Raspberry Pi phpMyAdmin run SQL table created.

After that, you should see your newly created table called `SensorData` in the `esp_data` database.

Raspberry Pi phpMyAdmin table created empty

### PHP Script HTTP POST – Insert Data in MySQL Database

In this section, we’re going to create a PHP script that is responsible for receiving incoming requests from the ESP32 or ESP8266 and inserting the data into a MySQL database.

You can either run the next commands on a Raspberry Pi set as a desktop computer or using an SSH connection.

If you’re connected to your Raspberry Pi with an SSH connection, type the next command to create a file in `/var/www/html` directory:

```
pi@raspberrypi:~ $ nano /var/www/html/post-esp-data.php
```
> Note: if you’re following this tutorial and you’re not familiar with PHP or MySQL, I recommend creating these exact files. Otherwise, you’ll need to modify the ESP sketch provided with different URL paths.

Copy the following PHP script to the newly created file (`post-esp-data.php`):

```php
<?php

/*
  Rui Santos
  Complete project details at https://RandomNerdTutorials.com/esp32-esp8266-mysql-database-php/
  
  Permission is hereby granted, free of charge, to any person obtaining a copy
  of this software and associated documentation files.
  
  The above copyright notice and this permission notice shall be included in all
  copies or substantial portions of the Software.
*/

$servername = "localhost";

// REPLACE with your Database name
$dbname = "REPLACE_WITH_YOUR_DATABASE_NAME";
// REPLACE with Database user
$username = "REPLACE_WITH_YOUR_USERNAME";
// REPLACE with Database user password
$password = "REPLACE_WITH_YOUR_PASSWORD";

// Keep this API Key value to be compatible with the ESP32 code provided in the project page. 
// If you change this value, the ESP32 sketch needs to match
$api_key_value = "tPmAT5Ab3j7F9";

$api_key= $sensor = $location = $value1 = $value2 = $value3 = "";

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $api_key = test_input($_POST["api_key"]);
    if($api_key == $api_key_value) {
        $sensor = test_input($_POST["sensor"]);
        $location = test_input($_POST["location"]);
        $value1 = test_input($_POST["value1"]);
        $value2 = test_input($_POST["value2"]);
        $value3 = test_input($_POST["value3"]);
        
        // Create connection
        $conn = new mysqli($servername, $username, $password, $dbname);
        // Check connection
        if ($conn->connect_error) {
            die("Connection failed: " . $conn->connect_error);
        } 
        
        $sql = "INSERT INTO SensorData (sensor, location, value1, value2, value3)
        VALUES ('" . $sensor . "', '" . $location . "', '" . $value1 . "', '" . $value2 . "', '" . $value3 . "')";
        
        if ($conn->query($sql) === TRUE) {
            echo "New record created successfully";
        } 
        else {
            echo "Error: " . $sql . "<br>" . $conn->error;
        }
    
        $conn->close();
    }
    else {
        echo "Wrong API Key provided.";
    }

}
else {
    echo "No data posted with HTTP POST.";
}

function test_input($data) {
    $data = trim($data);
    $data = stripslashes($data);
    $data = htmlspecialchars($data);
    return $data;
}
```

Before saving the file, you need to modify the `$dbname`, `$username` and `$password` variables with your unique details:

```php
// Your Database name
$dbname = "esp_data";
// Your Database user
$username = "root";
// Your Database user password
$password = "YOUR_USER_PASSWORD";
```

After adding the database name, username and password, save the file (Ctrl+X, y, and Enter key) and continue with this tutorial. 

### PHP Script – Display Database Content

Create another PHP file in the `/var/www/html` directory that will display all the database content in a web page. Name your new file: ```esp-data.php```

```
pi@raspberrypi:~ $ nano /var/www/html/esp-data.php
```
Edit the newly created file (esp-data.php) and copy the following PHP script:

```php
<!DOCTYPE html>
<html><body>
<?php
/*
  Rui Santos
  Complete project details at https://RandomNerdTutorials.com/esp32-esp8266-mysql-database-php/
  
  Permission is hereby granted, free of charge, to any person obtaining a copy
  of this software and associated documentation files.
  
  The above copyright notice and this permission notice shall be included in all
  copies or substantial portions of the Software.
*/

$servername = "localhost";

// REPLACE with your Database name
$dbname = "REPLACE_WITH_YOUR_DATABASE_NAME";
// REPLACE with Database user
$username = "REPLACE_WITH_YOUR_USERNAME";
// REPLACE with Database user password
$password = "REPLACE_WITH_YOUR_PASSWORD";

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);
// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
} 

$sql = "SELECT id, sensor, location, value1, value2, value3, reading_time FROM SensorData ORDER BY id DESC";

echo '<table cellspacing="5" cellpadding="5">
      <tr> 
        <td>ID</td> 
        <td>Sensor</td> 
        <td>Location</td> 
        <td>Value 1</td> 
        <td>Value 2</td>
        <td>Value 3</td> 
        <td>Timestamp</td> 
      </tr>';
 
if ($result = $conn->query($sql)) {
    while ($row = $result->fetch_assoc()) {
        $row_id = $row["id"];
        $row_sensor = $row["sensor"];
        $row_location = $row["location"];
        $row_value1 = $row["value1"];
        $row_value2 = $row["value2"]; 
        $row_value3 = $row["value3"]; 
        $row_reading_time = $row["reading_time"];
        // Uncomment to set timezone to - 1 hour (you can change 1 to any number)
        //$row_reading_time = date("Y-m-d H:i:s", strtotime("$row_reading_time - 1 hours"));
      
        // Uncomment to set timezone to + 4 hours (you can change 4 to any number)
        //$row_reading_time = date("Y-m-d H:i:s", strtotime("$row_reading_time + 4 hours"));
      
        echo '<tr> 
                <td>' . $row_id . '</td> 
                <td>' . $row_sensor . '</td> 
                <td>' . $row_location . '</td> 
                <td>' . $row_value1 . '</td> 
                <td>' . $row_value2 . '</td>
                <td>' . $row_value3 . '</td> 
                <td>' . $row_reading_time . '</td> 
              </tr>';
    }
    $result->free();
}

$conn->close();
?> 
</table>
</body>
</html>
```

Add the `$dbname`, `$username` and `$password`:

```php
// Your Database name
$dbname = "esp_data";
// Your Database user
$username = "root";
// Your Database user password
$password = "YOUR_USER_PASSWORD";
```
Save the file (Ctrl+X, y, and Enter key) and continue with this project.

If you try to access your Raspberry Pi IP Address in the following URL path: 

`http://Your-Raspberry-Pi-IP-Address/esp-data.php` 

That’s it! If you see that empty table printed in your browser, it means that everything is ready. In the next section, you’ll learn how to insert data from your ESP32 or ESP8266 into the database.

### Preparing Your ESP32 or ESP8266

We’ll program the ESP32/ESP8266 using Arduino IDE, so you must have the ESP32/ESP8266 add-on installed in your Arduino IDE. Follow one of the next tutorials depending on the board you’re using:

- Install the ESP32 Board in Arduino IDE  
- Install the ESP8266 Board in Arduino IDE

After installing the necessary board add-ons, copy the following code to your Arduino IDE, but don’t upload it yet. You need to make some changes to make it work for you.


Setting network credentials how indicated in webserver section.

**Setting your serverName**

You also need to type your Raspberry Pi IP address, so the ESP publishes the readings to your LAMP server.
```c
const char* serverName = "http://Your-Raspberry-Pi-IP-Address/post-esp-data.php";
```

For example:
```c
const char* serverName = "http://192.168.1.86/post-esp-data.php";
```

Now, you can upload the code to your board. It should work straight away both in the ESP32 or ESP8266 board. If you want to learn how the code works, read the next section.

**How the code works**

This project is already quite long, so we won’t cover in detail how the code works, but here’s a quick summary:

- Import all the libraries to make it work (it will import either the `ESP32` or `ESP8266` libraries based on the selected board in your Arduino IDE)
- Set variables that you might want to change (`apiKeyValue`, `sensorName`, `sensorLocation`)
- The `apiKeyValue` is just a random string that you can modify. It’s used for security reasons, so only anyone that knows your API key can publish data to your database
- Initialize the serial communication for debugging purposes
- Establish a Wi-Fi connection with your router
- Initialize the BME280 to get readings

Then, in the `loop()` is where you actually make the HTTP POST request every 30 seconds with the latest sensor readings:

```c
// Your Domain name with URL path or IP address with path
http.begin(serverName);

// Specify content-type header
http.addHeader("Content-Type", "application/x-www-form-urlencoded");

// Prepare your HTTP POST request data
String httpRequestData = "api_key=" + apiKeyValue + "&sensor=" + sensorName                      + "&location=" + sensorLocation + "&value1=" + String(bme.readTemperature())                      + "&value2=" + String(bme.readHumidity()) + "&value3=" + String(bme.readPressure()/100.0F) + "";

int httpResponseCode = http.POST(httpRequestData);
```

You can comment the `httpRequestData` variable above that concatenates all the sensors readings and use the `httpRequestData` variable below for testing purposes:

```c
String httpRequestData = "api_key=tPmAT5Ab3j7F9&sensor=BME280&location=Office&value1=24.75&value2=49.54&value3=1005.14";
```

After completing all the steps, let your ESP board collect some readings and publish them to your server.


For other information [randomnertutorials.com](https://randomnerdtutorials.com/esp32-esp8266-raspberry-pi-lamp-server/)

--- 

## Private domain server

The goal of this section is to have your own domain name and hosting account that allows you to store sensor readings from the ESP32 or ESP8266. You can visualize the readings from anywhere in the world by accessing your own server domain. 

<img src="https://github.com/mastroalex/tempcontrol/blob/main/diagram/web_server.jpg" alt="system" width="1000"/>


### Hosting Your PHP Application and MySQL Database 

In this guide is used Siteground hosting.

#### Preparing your MySQL Database

After signing up for a hosting account and setting up a domain name, you can login to your cPanel or similar dashboard. After that, follow the next steps to create your database, username, password and SQL table.

In my case, the database name is assigned by siteground. Save it.

<img src="https://github.com/mastroalex/tempcontrol/blob/main/privatedomain/database.png" alt="siteground_db" width="1000">


Add an user `username` and set a `password`. You must save all those details, because you’ll need them later to establish a database connection with your PHP code.

<img src="https://github.com/mastroalex/tempcontrol/blob/main/privatedomain/user.png" alt="siteground_user" width="1000">

Set the user to the database:

<img src="https://github.com/mastroalex/tempcontrol/blob/main/privatedomain/userselect.png" alt="userselect" width="1000">



That’s it! Your new database and user were created successfully. Now, save all your details because you’ll need them later:

- **Database name**: `example_esp_data`
- **Username**: `example_esp_board`
- **Password**: `your password`

#### Creating a SQL table

After creating your database and user, go back to cPanel dashboard and search for “phpMyAdmin”. 

In the left sidebar, select your database name `example_esp_data` and open the “SQL” tab

> Important: make sure you’ve opened the `example_esp_data` database. Then, click the SQL tab. If you don’t follow these exact steps and run the SQL query, you might create a table in the wrong database.

Copy the SQL query in the following snippet:

```sql
CREATE TABLE Sensor (
    id INT(6) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    value1 VARCHAR(10),
    value2 VARCHAR(10),
    value3 VARCHAR(10),
    reading_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
)
```

After that, you should see your newly created table called `Sensor` in the `example_esp_data` database as shown in the figure below:

#### PHP Script HTTP POST – Insert Data in MySQL Database

In this section, we’re going to create a PHP script that receives incoming requests from the ESP32 or ESP8266 and inserts the data into a MySQL database.

If you’re using a hosting provider with cPanel, you can search for “File Manager”.

Then, select the `public_html` option and press the “+ File” button to create a new .php file.

> Note: if you’re following this tutorial and you’re not familiar with PHP or MySQL, I recommend creating these exact files. Otherwise, you’ll need to modify the ESP sketch provided with different URL paths.

Create a new file in `/public_html` with this exact name and extension: `post-data.php`

PHP Create New file post data .php
Edit the newly created file (post-data.php) and copy the following snippet:
```php

<?php
/*
  Thanks to Rui Santos
  Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files.The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
*/

$servername = "localhost";

// REPLACE with your Database name
$dbname = "REPLACE_WITH_YOUR_DATABASE_NAME";
// REPLACE with Database user
$username = "REPLACE_WITH_YOUR_USERNAME";
// REPLACE with Database user password
$password = "REPLACE_WITH_YOUR_PASSWORD";

// Keep this API Key value to be compatible with the ESP32 code provided in the project page. If you change this value, the ESP32 sketch needs to match
$api_key_value = "tPmAT5Ab3j7F8";

$api_key = $value1 = $value2 = $value3 = "";

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $api_key = test_input($_POST["api_key"]);
    if($api_key == $api_key_value) {
        $value1 = test_input($_POST["value1"]);
        $value2 = test_input($_POST["value2"]);
        $value3 = test_input($_POST["value3"]);
        
        // Create connection
        $conn = new mysqli($servername, $username, $password, $dbname);
        // Check connection
        if ($conn->connect_error) {
            die("Connection failed: " . $conn->connect_error);
        } 
        
        $sql = "INSERT INTO Sensor (value1, value2, value3)
        VALUES ('" . $value1 . "', '" . $value2 . "', '" . $value3 . "')";
        
        if ($conn->query($sql) === TRUE) {
            echo "New record created successfully";
        } 
        else {
            echo "Error: " . $sql . "<br>" . $conn->error;
        }
    
        $conn->close();
    }
    else {
        echo "Wrong API Key provided.";
    }

}
else {
    echo "No data posted with HTTP POST.";
}

function test_input($data) {
    $data = trim($data);
    $data = stripslashes($data);
    $data = htmlspecialchars($data);
    return $data;
}

```

Before saving the file, you need to modify the `$dbname`, `$username` and `$password` variables with your unique details:

```php

// Your Database name
$dbname = "example_esp_data";
// Your Database user
$username = "example_esp_board";
// Your Database user password
$password = "YOUR_USER_PASSWORD";
```

After adding the database name, username and password, save the file and continue with this tutorial. If you try to access your domain name in the next URL path.
```
http://example.com/post-data.php
```

#### PHP Script – Visualize Database Content in a Chart

Create another PHP file in the `/public_html` directory that will plot the database content in a chart on a web page. Name your new file: `esp-chart.php`

Edit the newly created file (`esp-chart.php`) and copy the following code:

```php
<!--
  Thanks to Rui Santos
  -->
<?php

$servername = "localhost";

// REPLACE with your Database name
$dbname = "REPLACE_WITH_YOUR_DATABASE_NAME";
// REPLACE with Database user
$username = "REPLACE_WITH_YOUR_USERNAME";
// REPLACE with Database user password
$password = "REPLACE_WITH_YOUR_PASSWORD";

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);
// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
} 

$sql = "SELECT id, value1, value2, value3, reading_time FROM Sensor order by reading_time desc limit 40";

$result = $conn->query($sql);

while ($data = $result->fetch_assoc()){
    $sensor_data[] = $data;
}

$readings_time = array_column($sensor_data, 'reading_time');

// ******* Uncomment to convert readings time array to your timezone ********
/*$i = 0;
foreach ($readings_time as $reading){
    // Uncomment to set timezone to - 1 hour (you can change 1 to any number)
    $readings_time[$i] = date("Y-m-d H:i:s", strtotime("$reading - 1 hours"));
    // Uncomment to set timezone to + 4 hours (you can change 4 to any number)
    //$readings_time[$i] = date("Y-m-d H:i:s", strtotime("$reading + 4 hours"));
    $i += 1;
}*/

$value1 = json_encode(array_reverse(array_column($sensor_data, 'value1')), JSON_NUMERIC_CHECK);
$value2 = json_encode(array_reverse(array_column($sensor_data, 'value2')), JSON_NUMERIC_CHECK);
$value3 = json_encode(array_reverse(array_column($sensor_data, 'value3')), JSON_NUMERIC_CHECK);
$reading_time = json_encode(array_reverse($readings_time), JSON_NUMERIC_CHECK);

/*echo $value1;
echo $value2;
echo $value3;
echo $reading_time;*/

$result->free();
$conn->close();
?>

<!DOCTYPE html>
<html>
<meta name="viewport" content="width=device-width, initial-scale=1">
  <script src="https://code.highcharts.com/highcharts.js"></script>
  <style>
    body {
      min-width: 310px;
    	max-width: 1280px;
    	height: 500px;
      margin: 0 auto;
    }
    h2 {
      font-family: Arial;
      font-size: 2.5rem;
      text-align: center;
    }
  </style>
  <body>
    <h2>TEMPCONTROL</h2>
    <div id="chart-temperature" class="container"></div>
    <div id="chart-humidity" class="container"></div>
    <div id="chart-pressure" class="container"></div>
<script>

var value1 = <?php echo $value1; ?>;
var value2 = <?php echo $value2; ?>;
var value3 = <?php echo $value3; ?>;
var reading_time = <?php echo $reading_time; ?>;

var chartT = new Highcharts.Chart({
  chart:{ renderTo : 'chart-temperature' },
  title: { text: 'Temperature' },
  series: [{
    showInLegend: false,
    data: value1
  }],
  plotOptions: {
    line: { animation: false,
      dataLabels: { enabled: true }
    },
    series: { color: '#059e8a' }
  },
  xAxis: { 
    type: 'datetime',
    categories: reading_time
  },
  yAxis: {
    title: { text: 'Temperature (Celsius)' }
    //title: { text: 'Temperature (Fahrenheit)' }
  },
  credits: { enabled: false }
});

var chartH = new Highcharts.Chart({
  chart:{ renderTo:'chart-humidity' },
  title: { text: 'Humidity' },
  series: [{
    showInLegend: false,
    data: value2
  }],
  plotOptions: {
    line: { animation: false,
      dataLabels: { enabled: true }
    }
  },
  xAxis: {
    type: 'datetime',
    //dateTimeLabelFormats: { second: '%H:%M:%S' },
    categories: reading_time
  },
  yAxis: {
    title: { text: 'Humidity (%)' }
  },
  credits: { enabled: false }
});


var chartP = new Highcharts.Chart({
  chart:{ renderTo:'chart-pressure' },
  title: { text: 'Pressure' },
  series: [{
    showInLegend: false,
    data: value3
  }],
  plotOptions: {
    line: { animation: false,
      dataLabels: { enabled: true }
    },
    series: { color: '#18009c' }
  },
  xAxis: {
    type: 'datetime',
    categories: reading_time
  },
  yAxis: {
    title: { text: 'Pressure (hPa)' }
  },
  credits: { enabled: false }
});

</script>
</body>
</html>
```

After adding the `$dbname`, `$username` and `$password` save the file and continue with this project.

```php
// Your Database name
$dbname = "example_esp_data";
// Your Database user
$username = "example_esp_board";
// Your Database user password
$password = "YOUR_USER_PASSWORD";
```

For siteground user it is important to **disable dynamic cache** so add into `.htacces`the following snippet:

```
<IfModule mod_headers.c> 
Header set Cache-Control "max-age=0,no-store"
</IfModule>
```

The file is locatel in `/public_html` directory.
if the file does not exist, create it.

Try to access your domain name in the following URL path:

```
http://example.com/esp-chart.php
```

That’s it! If you see three empty charts in your browser, it means that everything is ready. In the next section, you’ll learn how to publish your ESP32 or ESP8266 sensor readings.

To build the charts, we’ll use the [Highcharts library](https://www.highcharts.com/docs/). We’ll create three charts: temperature, humidity and pressure over time. The charts display a maximum of 400 data points, and a new reading is added every 5 minutes, but you change these values in your code.

This is only an example code, the basis for making everything work. The code is completely modular and will be customized and expanded

### Preparing Your ESP32 or ESP8266

The same previous code is used with some additions:

Include new library:

```c
#include <ESP8266HTTPClient.h>;
```

Inizialize new variables:

```c
// server mysql
// REPLACE with your Domain name and URL path or IP address with path
const char* serverName = "http://yourdomain.it/post-data.php";
// Keep this API Key value to be compatible with the PHP code provided in the project page.
// If you change the apiKeyValue value, the PHP file /post-data.php also needs to have the same key
String apiKeyValue = "tPmAT5Ab3j7F8";
//timer 
unsigned long t2=0;
unsigned long dt=0;
unsigned long t3 = 300000;//update database every 5 minutes
```

Add this snippet in the `loop()`section:

```c
//Check WiFi connection status
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;

    // Your Domain name with URL path or IP address with path
    http.begin(serverName);

    // Specify content-type header
    http.addHeader("Content-Type", "application/x-www-form-urlencoded");
    dt=millis()-t2;
    if (dt>= t3) {
      // Prepare your HTTP POST request data
      String httpRequestData = "api_key=" + apiKeyValue + "&value1=" + String(t)
                               + "&value2=" + String(h)  + "";
      Serial.print("httpRequestData: ");
      Serial.println(httpRequestData);
 
      if (httpResponseCode > 0) {
        Serial.print("HTTP Response code: ");
        Serial.println(httpResponseCode);
      }
      else {
        Serial.print("Error code: ");
        Serial.println(httpResponseCode);
      }
      t2=millis();
    }
    // Free resources
    http.end();
  }
```

> Only `value1` and `value2` are used in this code. For `value3` see in the personalization section.

You need to make some changes to make it work for you.

Setting network credentials how indicated in webserver section.

**Setting your serverName**

You also need to type your domain name, so the ESP publishes the readings to your own server.

```c
const char* serverName = "http://example.com/post-data.php";
```

Now, you can upload the code to your board. It should work straight away both in the ESP32 or ESP8266 board. If you want to learn how the code works, read the next section.

**How the code works**

This project is already quite long, so we won’t cover in detail how the code works, but here’s a quick summary:

- Import all the libraries to make it work (it will import either the ESP32 or ESP8266 libraries based on the selected board in your Arduino IDE)
- Set variables that you might want to change (`apiKeyValue`)
- The `apiKeyValue` is just a random string that you can modify. It’s used for security reasons, so only anyone that knows your API key can publish data to your database
- Initialize the serial communication for debugging purposes
- Establish a Wi-Fi connection with your router
- Initialize the sensor to get readings

Then, in the `loop()` is where you actually make the HTTP POST request every 30 seconds with the latest BME280 readings:

```c
// Your Domain name with URL path or IP address with path
http.begin(serverName);

// Specify content-type header
http.addHeader("Content-Type", "application/x-www-form-urlencoded");

// Prepare your HTTP POST request data
String httpRequestData = "api_key=" + apiKeyValue + "&value1=" 
                       + String(bme.readTemperature()) 
                       + "&value2=" + String(bme.readHumidity()) 
                       + "&value3=" + String(bme.readPressure()/100.0F) + "";

int httpResponseCode = http.POST(httpRequestData);
```

### Code complete

After completing all the steps, let your ESP board collect some readings and publish them to your server.

If everything is correct, you should see data in Arduino IDE Serial Monitor.

If you open your domain name in this URL path:
```
http://example.com/esp-chart.php
```

You should see the all the readings stored in your database. Refresh the web page to see the latest readings:


<img src="https://github.com/mastroalex/tempcontrol/blob/main/privatedomain/graph.png" alt="graph" width="1000">
 
You can also go to phpMyAdmin to manage the data stored in your Sensor table. You can delete it, edit, etc…

> Thanks to [randomnerdtutorials.com](https://randomnerdtutorials.com/visualize-esp32-esp8266-sensor-readings-from-anywhere/)

### Personalize it

#### Send data from another ESP8266

Just load the same code and edit the following part in the `loop()` :

```c
if (dt>= t3) {
      // Prepare your HTTP POST request data
      String httpRequestData = "api_key=" + apiKeyValue + "&value3=" + String(t)
                               + "&value4=" + String(h)  + "";
      Serial.print("httpRequestData: ");
      Serial.println(httpRequestData);

      if (httpResponseCode > 0) {
        Serial.print("HTTP Response code: ");
        Serial.println(httpResponseCode);
      }
      else {
        Serial.print("Error code: ");
        Serial.println(httpResponseCode);
      }
      t2=millis();
    }
    // Free resources
    http.end();
  }
```

Only for testing it is recommended to reduce `t3` time to 3000 ms. After testing reset it to 300000 (5 minutes).

#### Add new column in table

To add new column login in phpMyAdmin, go to the left sidebar and click the name of the database table you want to add a column to.

Click on `Structure`and there is `aggiungi # campo`.

<img src="https://github.com/mastroalex/tempcontrol/blob/main/privatedomain/phpmyadmin1.png" alt="php" width="1000">

Enter the number of columns and select location. Than click go.

Insert column detail:
- Name: `value4`
- Type:`VARCHAR`
- Length/values: `10`
- Collation: `utf8mb4_general_ci	`

<img src="https://github.com/mastroalex/tempcontrol/blob/main/privatedomain/phpmyadmin2.png" alt="php" width="1000">

#### Modify post-data.php

Add `value4` in the initial section:
```php

$api_key = $value1 = $value2 = $value3 = $value4 = "";
```

Add it in the `if` cycle:
```php
if($api_key == $api_key_value) {
        $value1 = test_input($_POST["value1"]);
        $value2 = test_input($_POST["value2"]);
        $value3 = test_input($_POST["value3"]);
        $value4 = test_input($_POST["value4"]);

        // Create connection
        $conn = new mysqli($servername, $username, $password, $dbname);
        // Check connection
        if ($conn->connect_error) {
            die("Connection failed: " . $conn->connect_error);
        } 
        
        $sql = "INSERT INTO Sensor (value1, value2, value3, value4)
        VALUES ('" . $value1 . "', '" . $value2 . "', '" . $value3 . "','" . $value4 . "')";
        
        if ($conn->query($sql) === TRUE) {
            echo "New record created successfully";
        } 
        else {
            echo "Error: " . $sql . "<br>" . $conn->error;
        }
    
        $conn->close();
    }
```

Do it for all the extra values.

Before continue and add chart verify in phpMyAdmin that the table is correctly upgrade with new values.

<img src="https://github.com/mastroalex/tempcontrol/blob/main/privatedomain/phpmyadmin3.png" alt="php" width="1000">

> In this screen there are no values for `value1` and `value2` because they are set to update every 5 minutes instead of `value3` and `value4` which update every 3 seconds. It is for testing purposes only.

#### Add new graph

To add new chart edit `esp-chart.php`.

1. Just add `value4`in the section where is also `value1`, `value2`and `value4`.

2. Duplicate the `<div>` container.

3. Duplicate the Javscript section and set parameters.

> **Warning!** Select a number of maximum values that are actually in the table otherwise the graph will give problems. 
```php
$sql = "SELECT id, value1, value2, value3, value4, reading_time FROM Sensor order by reading_time desc limit 400";
```
Set a low number and increase progressively(otherwise check how many values there are from the table).

<img src="https://github.com/mastroalex/tempcontrol/blob/main/privatedomain/finalchart.png" alt="php" width="1000">

So the code look like this:
```php
<?php

$servername = "localhost";

// REPLACE with your Database name
$dbname = "DATABASE_NAME";
// REPLACE with Database user
$username = "USER_NAME";
// REPLACE with Database user password
$password = "PASSWORD";

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);
// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
} 

$sql = "SELECT id, value1, value2, value3, value4, reading_time FROM Sensor order by reading_time desc limit 400";

$result = $conn->query($sql);

while ($data = $result->fetch_assoc()){
    $sensor_data[] = $data;
}

$readings_time = array_column($sensor_data, 'reading_time');

$value1 = json_encode(array_reverse(array_column($sensor_data, 'value1')), JSON_NUMERIC_CHECK);
$value2 = json_encode(array_reverse(array_column($sensor_data, 'value2')), JSON_NUMERIC_CHECK);
$value3 = json_encode(array_reverse(array_column($sensor_data, 'value3')), JSON_NUMERIC_CHECK);
$value4 = json_encode(array_reverse(array_column($sensor_data, 'value4')), JSON_NUMERIC_CHECK);

$reading_time = json_encode(array_reverse($readings_time), JSON_NUMERIC_CHECK);
$result->free();
$conn->close();
?>

<!DOCTYPE html>
<html>
<meta name="viewport" content="width=device-width, initial-scale=1">
  <script src="https://code.highcharts.com/highcharts.js"></script>
  <script src="https://code.highcharts.com/highcharts.js"></script>
  <script src="https://code.highcharts.com/modules/data.js"></script>
  <script src="https://code.highcharts.com/modules/exporting.js"></script>
  <script src="https://code.highcharts.com/modules/export-data.js"></script>
  <script src="https://code.highcharts.com/modules/accessibility.js"></script>
  <style>
    body {
      min-width: 310px;
    	max-width: 1280px;
    	height: 500px;
      margin: 0 auto;
    }
    h2 {
      font-family: Arial;
      font-size: 2.5rem;
      text-align: center;
    }
  </style>
  <body>
    <h2>Temp control</h2>
    <div id="chart-temperature" class="container"></div>
    <div id="chart-humidity" class="container"></div>
    <div id="chart-temperature2" class="container"></div>
    <div id="chart-humidity2" class="container"></div>


<script>

var value1 = <?php echo $value1; ?>;
var value2 = <?php echo $value2; ?>;
var value3 = <?php echo $value3; ?>;
var value4 = <?php echo $value4; ?>;
var reading_time = <?php echo $reading_time; ?>;



var chartT = new Highcharts.Chart({
  chart:{ renderTo : 'chart-temperature',
        zoomType: 'x',
        panning: true,
        panKey: 'shift' },
  title: { text: 'Temperatura camera Ale' },
  series: [{
    showInLegend: false,
    data: value1
  }],
  plotOptions: {
    line: { animation: false,
      dataLabels: { enabled: true }
    },
    series: { color: '#059e8a' }
  },
  xAxis: { 
    type: 'datetime',
    categories: reading_time
  },
  yAxis: {
    title: { text: 'Temperature (Celsius)' }
    //title: { text: 'Temperature (Fahrenheit)' }
  },
  credits: { enabled: false }
});

var chartH = new Highcharts.Chart({
  chart:{ renderTo:'chart-humidity2',
           zoomType: 'x',
           panning: true,
           panKey: 'shift'},
  title: { text: 'Umidità camera Ale' },
  series: [{
    showInLegend: false,
    data: value4
  }],
  plotOptions: {
    line: { animation: false,
      dataLabels: { enabled: true }
    }
  },
  xAxis: {
    type: 'datetime',
    //dateTimeLabelFormats: { second: '%H:%M:%S' },
    categories: reading_time
  },
  yAxis: {
    title: { text: 'Humidity (%)' }
  },
  credits: { enabled: false }
});

var chartH = new Highcharts.Chart({
  chart:{ renderTo:'chart-humidity',
           zoomType: 'x',
           panning: true,
           panKey: 'shift'},
  title: { text: 'Umidità camera Ale' },
  series: [{
    showInLegend: false,
    data: value2
  }],
  plotOptions: {
    line: { animation: false,
      dataLabels: { enabled: true }
    }
  },
  xAxis: {
    type: 'datetime',
    //dateTimeLabelFormats: { second: '%H:%M:%S' },
    categories: reading_time
  },
  yAxis: {
    title: { text: 'Humidity (%)' }
  },
  credits: { enabled: false }
});


var chartT = new Highcharts.Chart({
  chart:{ renderTo : 'chart-temperature2',
        zoomType: 'x',
        panning: true,
        panKey: 'shift' },
  title: { text: 'Temperatura cucina' },
  series: [{
    showInLegend: false,
    data: value3
  }],
  plotOptions: {
    line: { animation: false,
      dataLabels: { enabled: true }
    },
    series: { color: '#059e8a' }
  },
  xAxis: { 
    type: 'datetime',
    categories: reading_time
  },
  yAxis: {
    title: { text: 'Temperature (Celsius)' }
    //title: { text: 'Temperature (Fahrenheit)' }
  },
  credits: { enabled: false }
});
</script>
</body>
</html>

```
#### Optimize it 

To avoid discrepancies between data and time interval i suggest to create different table for each esp8266.

So create new table like [previous section](https://github.com/mastroalex/tempcontrol#creating-a-sql-table) with different name.
```sql
CREATE TABLE Sensor2 (
    id INT(6) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    value5 VARCHAR(10),
    value6 VARCHAR(10),
    reading_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
)
```

Copy `post-data.php` into new file like `post-data1.php` and change table name.

```php
$sql = "INSERT INTO Sensor2 (value5, value6)
        VALUES ('" . $value5 . "', '" . $value6 . "')";
```
Change also `serverName` in the ESP8266 sketch.
```c
const char* serverName = "http://yourdomain.it/post-data1.php";
```

Now is possible to control that the table Sensor2 is effectively update with new data.
If everything is alright open `esp-chart.php` and duplicate the connection to the sql server and change the second table name in `Sensor2`. 

So the code look like this:

```php
<?php

$servername = "localhost";

// REPLACE with your Database name
$dbname = "DBNAME";
// REPLACE with Database user
$username = "USERNAME";
// REPLACE with Database user password
$password = "PASSWORD";

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);
// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
} 

$sql = "SELECT id, value1, value2, value3, value4, reading_time FROM Sensor order by reading_time desc limit 40";

$result = $conn->query($sql);

while ($data = $result->fetch_assoc()){
    $sensor_data[] = $data;
}

$readings_time = array_column($sensor_data, 'reading_time');

$value1 = json_encode(array_reverse(array_column($sensor_data, 'value1')), JSON_NUMERIC_CHECK);
$value2 = json_encode(array_reverse(array_column($sensor_data, 'value2')), JSON_NUMERIC_CHECK);
$value3 = json_encode(array_reverse(array_column($sensor_data, 'value3')), JSON_NUMERIC_CHECK);
$value4 = json_encode(array_reverse(array_column($sensor_data, 'value4')), JSON_NUMERIC_CHECK);

$reading_time = json_encode(array_reverse($readings_time), JSON_NUMERIC_CHECK);

$result->free();
$conn->close();

// second table Sensor2

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);
// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
} 

$sql = "SELECT id, value5, value6, reading_time FROM Sensor2 order by reading_time desc limit 40";

$result = $conn->query($sql);

while ($data = $result->fetch_assoc()){
    $sensor_data[] = $data;
}

$readings_time2 = array_column($sensor_data, 'reading_time');

$value6 = json_encode(array_reverse(array_column($sensor_data, 'value6')), JSON_NUMERIC_CHECK);
$value5 = json_encode(array_reverse(array_column($sensor_data, 'value5')), JSON_NUMERIC_CHECK);

$reading_time2 = json_encode(array_reverse($readings_time), JSON_NUMERIC_CHECK);

$result->free();
$conn->close();

?>
```
#### Time intervals 

It is interesting to be able to observe the graph for different time intervals.
To do it copy `esp-chart.php` into new file and change the following lines:
```php
$sql = "SELECT id, value1, value2, value3, reading_time FROM Sensor3 order by reading_time desc limit 24";
```
In this case the number `24` represents 2 hours of data reading. As it is set up (`t3=300000`) the sensor uploads a reading to the server every 5 minutes. It is possible to set different times intervals.
Set:
- `72` for 6 hours and save it as `esp-chart-1d.php`
- `288` for 24 hours `esp-chart-1d.php`

Now add buttons:

Add some css style into `esp-chart.php`:
```css
 .containerbt { 
    height: 100px;
    position: relative;
    }

    .center {
      margin: 0;
      position: absolute;
      top: 50%;
      left: 50%;
      -ms-transform: translate(-50%, -50%);
      transform: translate(-50%, -50%);
    }
    .button {
      position: relative;
      background-color: #4CAF50;
      border: none;
      font-size: 20px;
      color: #FFFFFF;
      padding: 20px;
      width: 150px;
      text-align: center;
      -webkit-transition-duration: 0.4s; /* Safari */
      transition-duration: 0.4s;
      text-decoration: none;
      overflow: hidden;
      cursor: pointer;
      border-radius: 10px;
    }

    .button:after {
      content: "";
      background: #90EE90;
      display: block;
      position: absolute;
      padding-top: 300%;
      padding-left: 350%;
      margin-left: -20px!important;
      margin-top: -120%;
      opacity: 0;
      transition: all 0.8s
    }

    .button:active:after {
      padding: 0;
      margin: 0;
      opacity: 1;
      transition: 0s
    }
  .button:hover {background-color: #3e8e41}
```

And add link in the `<body>`section:
```html
<h2>Temp control</h2>
    <div class="containerbt">
      <div class="center">
        <a class="button" href="http://tempcontrol.it/esp-chart-6h.php">6 Ore</a>
        <a class="button" href="http://tempcontrol.it/esp-chart-1d.php">24 Ore</a>
      </div>
    </div>
    <div id="chart-temperature" class="container"></div>
    <div id="chart-humidity" class="container"></div>
    <div id="chart-temperature2" class="container"></div>
    <div id="chart-humidity2" class="container"></div>
    <div id="chart-temperature4" class="container"></div>
    <div id="chart-temperature3" class="container"></div>
```
Look like this and it is fully personalizable.

<img src="https://github.com/mastroalex/tempcontrol/blob/main/privatedomain/finalchart2.png" alt="chart" width="1000">

#### Graph 

The graph is based on [Highchart software](highcharts.com) interactive JavasSript chart.

This chart is fully personalizable.

To edit chart properties you have to change Javascript code section. 

This section is the snippet like:

```js
var chartH = new Highcharts.Chart({
  chart:{ renderTo:'chart-humidity'
          },
  title: { text: 'Umidità camera Ale' },
  series: [{
    showInLegend: false,
    data: value2
  }],
  plotOptions: {
    line: { animation: false,
      dataLabels: { enabled: true }
    }
  },
  xAxis: {
    type: 'datetime',
    //dateTimeLabelFormats: { second: '%H:%M:%S' },
    categories: reading_time
  },
  yAxis: {
    title: { text: 'Humidity (%)' }
  },
  credits: { enabled: false }
});
```

##### Make graph zoomable and scrollable

Ad in the php file `esp-chart.php` the following snippet in the `chart:{ }` section:
```js
chart:{ renderTo : 'chart-temperature',
        zoomType: 'x',
        panning: true,
        panKey: 'shift' },
```

This allow to select area and zoom in. It is also possible to pan chart by continue pressing `shift` and scrolling with mouse.

<img src="https://github.com/mastroalex/tempcontrol/blob/main/privatedomain/zoom1.png" alt="zoom" width="1000">

<img src="https://github.com/mastroalex/tempcontrol/blob/main/privatedomain/zoom2.png" alt="zoom" width="1000">

It work also on mobile device.

##### Remove datalabel

If there is a lot of data it may be useful to remove the labels

Set `false` in:
```js
plotOptions: {
    line: { animation: false,
      dataLabels: { enabled: false }
    }
```

#### Privacy 

There are several ways to secure this page. Most are safer but more complex than the one presented.
A simple method is to add the following code and add the part of the code related to the charts inside it.

```html
<?php
// -------------------------
$password = '123456789';
// -------------------------

$pwd = isset($_GET['pwd']) ? $_GET['pwd'] : false;
if (!$pwd || $pwd != $password) {
  ?>
<form method="get" action="<?php echo $_SERVER['PHPSELF']; ?>">
<table border="0" cellspacing="0" cellpadding="10">
<?php if ($pwd !== false): ?><tr class="errore"><td colspan="3">La password inserita non è corretta!</td></tr><?php endif; ?>
<tr>
  <td>Password</td>
  <td><input type="password" name="pwd" style="width: 180px;"/></td>
  <td><input type="submit" value="Entra"/></td>
</tr>
</table>
</form>  
  <?php  
}else{
  ?>
...
... div_section_for_charts... 
...
  <?php  
}
?>
```

Insert your password in `YOURPASSWORD`.

Put this snippet in place of the `<div>` of the charts and insert the graphs `<div>` in place of the `div_section_for_charts... `.

--- 


## Tasmota smart control

It is worth mentioning the possibility of using the tasmota firmware to transform the esp 8266 into an intelligent actuator. With this opnesource firmware we can easily control and update the devices locally. We can also use timers and rules to automate everything.

In this case we use it to add 6 home automation outputs to control 6 relays at 220 V. we use a multiple relay board with 5V power supply and 3.3V-5V logic converter.

These smart outputs integrate easily with alexa to other alexa home automation systems.

The best way to setup tasmota firmware is to download [tasmotizer](https://github.com/tasmota/tasmotizer). 
Install from PyPI using pip:
- Upgrade pip and wheel packages: `pip3 install --upgrade pip wheel`
- Install tasmotizer: `pip3 install tasmotizer`
- Then simply run `tasmotizer.py` from the shell/command line

Now download latest firmare `.bin` and load it by press Tasmotize!

<img src="https://github.com/mastroalex/tempcontrol/blob/main/diagram/tasmotize.png" alt="tasmotize" width="400">

Now connect directly to the devices access point and configure wireless settings. 
After successful Internet connections, access the local IP of the devices from the browser.
Set the `Template` with pin for `user` use and set the `Module` by connecting pin with numeric index. This numeric index rapresent the smart output.

<img src="https://github.com/mastroalex/tempcontrol/blob/main/diagram/tasmota1.png" alt="tasmota" width="250"> <img src="https://github.com/mastroalex/tempcontrol/blob/main/diagram/tasmota2.png" alt="tasmota" width="250">

It is necessary to set the output as relay if you want to use the output as a switch or you can set it as a button or with many other options.

Now it is possibile to controll the output from web server and connect it with Alexa by emulating Philips Hue Sistem (ad new devices, select `Philips Hue Bridge V1`). it is also possibile to set timer or clever rules. 

The web server:

<img src="https://github.com/mastroalex/tempcontrol/blob/main/diagram/tasmota.png" alt="tasmota" width="250">

It is also important to set the right time zone so open the console from web server and:
- `NtpServer`, to verify that the device can read time 
- `TimeSTD 0, 0, 10, 1, 3, 60` and `TimeDST 0, 0, 3, 1, 2, 120` to set winter (STD) and summer (DST) time
- `Timezone 99`, to disable the deviation from UTC leaving valid the commands for STD and DST time 

For other info [tasmota.com](https://tasmota.github.io/docs/).

For schematic:




# Future implementations

# Other project

- [TCS - Solar Heating Control Unit](https://github.com/mastroalex/TCS)
- [Energy monitor](https://github.com/mastroalex/energy)
- [Irrigation](https://github.com/mastroalex/irrigation)
- [Healt monitor](https://github.com/mastroalex/healt)


# Contributors 

- @mastroalex
- Graphic Design - @Alina Elena Mihai

---

- Hosting and server setting [@letoinformatics](https://letoinformatics.com/)
- Supplies of material and technical help [@termoclimaservice](https://termoclimaservice.net/)
 
# References 
- [ESP32/ESP8266 Publish Data to Raspberry Pi LAMP Server](https://randomnerdtutorials.com/esp32-esp8266-raspberry-pi-lamp-server/)
- [ESP8266 DS18B20 ](https://randomnerdtutorials.com/esp8266-ds18b20-temperature-sensor-web-server-with-arduino-ide/)
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
- [Why is lighting in the workplace important?](https://www.ilo.org/wcmsp5/groups/public/@americas/@ro-lima/@sro-port_of_spain/documents/presentation/wcms_250198.pdf)
- [Lighting Ergonomics - General](https://www.ccohs.ca/oshanswers/ergonomics/lighting_general.html)
- [Eye Discomfort in the Office](https://www.ccohs.ca/oshanswers/ergonomics/office/eye_discomfort.html)
- [Lighting Ergonomics - Survey and Solutions](https://www.ccohs.ca/oshanswers/ergonomics/lighting_survey.html)
- [3 basics type of lighting](https://www.ilepower.com/blogs/blog/the-three-basic-types-of-lighting)
- []()
