# Web server for ESP 8266 

## Introduction 

As indicated in the main file:

The web server can be accessed with any device that has a browser on your local network.
This is an asynchronous web server that update data automatically without need to refresh the web page and with custom CSS to style the web page.

<img src="https://github.com/mastroalex/tempcontrol/blob/main/esp8266webserveinfo/webserver_example.png" alt="system" width="700"/>

> DS18B20 hand held to see the difference

Install the DHT library bi Adafruit.

Follow the next step to install the ESPAsyncWebServer library:
1. [Download AsyncWebServer library](https://github.com/me-no-dev/ESPAsyncWebServer/archive/master.zip)
2. Unzip the .zip folder
3. Rename your folder from ~~ESPAsyncWebServer-master~~ to ESPAsyncWebServer
4. Move the ESPAsyncWebServer folder to your Arduino IDE installation libraries folder

Follow the next step to install the ESPAsync TCP Library:
1. [Download ESPAsync TCP library](https://github.com/me-no-dev/ESPAsyncWebServer/archive/master.zip)
2. Unzip
3. Rename your folder from ~~ESPAsyncTCP-master~~ to ESPAsyncTCP
4. Move the ESPAsyncTCP folder to your Arduino IDE installation libraries folder
Restard Arduino IDE.

The example code is presented in [COMPLETARE LINK AL FILE E SALVARE FILE PERSONALIZZATO DI ESEMPIO SENZA PW WIFI]()

Insert wifi credentials!
```c
const char* ssid = "REPLACE_WITH_YOUR_SSID";
const char* password = "REPLACE_WITH_YOUR_PASSWORD";
```

## Code comments 


## Complete code