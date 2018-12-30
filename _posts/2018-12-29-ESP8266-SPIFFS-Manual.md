---
layout: post
title:  "ESP8266 SPIFFS ESPTool"
date:   2018-12-29
---
# ESP8266 SPIFFS Uploading using ESPTool and MKSpiffs

In this tutorial, we will discuss how to make your own SPIFFS binary and upload it using ESPTool.

## Why make your own? Won't the Arduino IDE always work?

Yes and no. Using the Arduino IDE method is tried and true, its the simplest way. But if you are like me you will soon find out, sometimes you need to make your own binary.

Recently I had to make my own due to the limit of the file names.
By default you can only have file system names up to 32 bits long. Which if you have long names for thing such as fonts you may quickly run into that limit
So how do you get around it?

You make your own.

Its not as hard as you may expect, its a pretty simple process. You need to compile your own version of MKSPIFFS and then compile your own binary. And then you use ESPTool.py to upload it to your ESP8266.

Sounds easy right? Yeah. Most of the time compiling programs seems hard especially if you are new to this. But it is a lot easier than you expect.

There are a few things to know first:

* You need to use Windows 10 or Linux. You will be compiling with a make files and GCC.
* Basic command line usage
* You need to know info about the ESP8266 you are uploading it to. Model, Flash Size, etc.

I will be providing my own compiled binaries for mkspiffs for you to use [here](https://github.com/coldreactor64/ESP8266-Tutorials)

If you choose to use that, you may now go to the using mkspiffs section.

## Compiling MKSPIFFS

### Ubuntu users:
Install build essentials if you do not have it installed with the command:

  ```sudo apt-get install build-essential```

---
### Windows 10 users:

  Follow the instructions on this [page](https://docs.microsoft.com/en-us/windows/wsl/install-win10) to install Linux Subsystem for Windows.

  Install and open Ubuntu from Windows 10 and create the user.

  Install build essentials
  
  ```sudo apt-get install build-essential```

---
After installing the required items above, using git clone mkspiffs:

```git clone https://github.com/igrr/mkspiffs.git```

Move your directory to the cloned repository:

 ``` cd ./mkspiffs```

In that directory run this command:

 ```git submodule update --init```


If you are on windows switch to the Ubuntu console if you have not already.


We now can customize the making of this mkspiffs program.

There are a few options you can change:

  * SPIFFS_OBJ_NAME_LEN
  * SPIFFS_OBJ_META_LEN
  * SPIFFS_USE_MAGIC
  * SPIFFS_USE_MAGIC_LENGTH
  * SPIFFS_ALIGNED_OBJECT_INDEX_TABLES

  The best way I have found to change them is to edit the ```build_all_configs.sh``` file.

  For example if you were to need to expand ```SPIFFS_OBJ_NAME_LEN``` to ``64`` from the default of ``32``. You would go in and edit the file like this

````C
  # Arduino ESP8266
  make clean
  make dist BUILD_CONFIG_NAME="-arduino-esp8266" \
    CPPFLAGS="-DSPIFFS_USE_MAGIC_LENGTH=0 -DSPIFFS_ALIGNED_OBJECT_INDEX_TABLES=1 -DSPIFFS_OBJ_NAME_LEN=64"
````

adding the flag:
````C
-DSPIFFS_OBJ_NAME_LEN=64
````

You can now run the command

````bash
./build_all_configs.sh
````
Which will now build the 3 versions.


If you have it done successfully you should see 3 new folders

````bash
mkspiffs-0.2.3-5-g9f4acb5-arduino-esp32-linux64
mkspiffs-0.2.3-5-g9f4acb5-arduino-esp8266-linux64
mkspiffs-0.2.3-5-g9f4acb5-esp-idf-linux64
````

To check that everything worked and you have expanded your name length

Move folders to ```mkspiffs-0.2.3-5-g9f4acb5-arduino-esp8266-linux64```

````bash

cd ./mkspiffs-0.2.3-5-g9f4acb5-arduino-esp8266-linux64

````

and run

````bash

./mkspiffs --version

````

and you should see this as an output

````bash

SPIFFS ver. 0.3.7-5-gf5e26c4
Extra build flags: -DSPIFFS_USE_MAGIC_LENGTH=0 -DSPIFFS_ALIGNED_OBJECT_INDEX_TABLES=1 -DSPIFFS_OBJ_NAME_LEN=64
SPIFFS configuration:
  SPIFFS_OBJ_NAME_LEN: 64
  SPIFFS_OBJ_META_LEN: 0
  SPIFFS_USE_MAGIC: 1
  SPIFFS_USE_MAGIC_LENGTH: 0
  SPIFFS_ALIGNED_OBJECT_INDEX_TABLES: 1

````

and you will have successfully built mkspiffs with extra flags.


## Using MKSPIFFS

---
Disclamer: To use my compiled binary or your compiled binary please use Ubuntu or Linux for Windows Subsystem. 

For instructions for installation of the latter, they are at the beginning of the previous section.

---

### Gathering the data you need

Each ESP8266 has certain paremeters to create spiffs

In the board file, which you can find [here](https://github.com/esp8266/Arduino/blob/master/boards.txt) you need to look for your board and under it you will find

- Block size
- Page size
- Spiffs start
- Spiffs end

Next take the Spiffs end value and subtract the start value from it.

In my case with a ESP12E it is ```0x3FB000 - 0x100000``` which results in the Image size being ``0x3FB000``

Note these down as you will need these for the next part.

### Creating the SPIFFS.bin

Just like the Arduino IDE SPIFFS upload, put all of the needed content in a file called data and put the folder in the same directory as mkspiffs.


Next you must use the console to travel to the folder with the compiled binary

````bash

cd ./mkspiffs-0.2.3-5-g9f4acb5-arduino-esp8266-linux64

````

After you have done that you will need to use to create the command to create the bin file

The layout of the command is

````bash

./mkspiffs --size <image size> --page <page size> --block <block size> -d 5 --create ./data spiffs.bin

````
Enter the values you got above and execute the command and if all went sucessfully, you should have a bin file to upload.



## Uploading using esptool.py 

First you will need Python 2.7 or 3.4 or higher installed onto your system

If installed execute the command

````bash

pip install esptool

````
Once that is installed you are ready to upload.

In the file with the SPIFFS.bin run the command

````bash

esptool.py --baud <baud rate> --port <Serial Port> write_flash <SPIFFS Start> ./spiffs.bin -u

````

Insert the baud rate, Serial port, and the Start Address of SPIFFS on your board and execute and you will upload


## Disclamer

I am not responsible for any damages caused by uploading a wrongly configured SPIFFS. If your ESP8266 encounters errors from it; use the ESP8266 upload tool for arduino and upload a blank file system to the ESP8266 and that should in most cases fix any issues.