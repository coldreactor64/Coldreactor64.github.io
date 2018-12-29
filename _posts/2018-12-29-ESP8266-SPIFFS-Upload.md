---
layout: post
title:  "ESP8266 SPIFFS Uploading"
date:   2018-12-29
---
# ESP8266 SPIFFS Uploading

In this tutorial, we will discuss the SPIFFS filesystem and how to upload it using the Arduino IDE and using ESPTool

## ESP8266 SPIFFS Filesystem overview


The SPIFFS Filesystem is a easily accessible aboard most ESP8266 boards.

It commonly comes in sizes up to 3MB on boards with 4MB (32Mb) Flash Chips such as the ESP-12E which I will be using in this tutorial.

Many other boards have varying SPIFFS limits which you can check at the offical documentation [here](https://arduino-esp8266.readthedocs.io/en/latest/filesystem.html)

Flash Contents:

    |--------------|-------|---------------|--|--|--|--|--|
    ^              ^       ^               ^     ^
    Sketch    OTA update   File system   EEPROM  WiFi config (SDK)

The SPIFFS Filesystem is stored alongside the other flash contents.
This allows you to upload files for use in a ESP8266 web server, or allow you to store configuration files without risking writing over writing other contents like the sketch or OTA update sector.

## Arduino IDE SPIFFS uploading

For the Arduino IDE you can get an amazing tool which allows you to upload to it from your sketch file.

The tool is located [here](https://github.com/esp8266/arduino-esp8266fs-plugin/releases/latest)


1. Download the tool at this [link](https://github.com/esp8266/arduino-esp8266fs-plugin/releases/latest) 
2. In your Arduino sketchbook directory, create `tools` directory if you have not created one yet.
3. Extract the tool to the tools folder so the folders now look like ``<sketchbook>/tools/ESP8266FS/tool/``
4. Restart the Arduino IDE

You should now see the ESP8266 Sketch Data Upload

  ![SPIFFS Tool](/assets/images/2018/12/29/ESP8266SPIFFS/ArduinoIDESPIFFSUpload.PNG)
