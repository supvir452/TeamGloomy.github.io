---
title: Connecting a GTR via Wifi
tags: []
keywords: 
last_updated: 23/05/2021
summary: "Connecting a GTR via Wifi"
sidebar: mydoc_sidebar
permalink: gtr_connected_wifi.html
folder: mydoc
comments: false
toc: false
datatable: true
---

## Overview

The GTR is an STM32F407ZGT6 based board. 

## Firmware File

Choose the correct corresponding firmware (firmware-stm43f4-esp8266wifi.bin) from [here](https://github.com/gloomyandy/RepRapFirmware/releases). Remember to rename it to firmware.bin. Put it in the root of a FAT32 formatted SD card.   

## Wifi

Use a nodemcu ESP8266 with USB programming as it already 5v tolerant and it allows for updating via USB.

{% include tip.html content="If you would prefer a premade adapter to enable WiFi, TeamGloomy have created a plugin WiFi adapter board for the GTR. It can be purchased on tindie [here](https://www.tindie.com/products/pcr/reprapfirmware-wifi-adapterboard-for-gtr/) " %}

### BOM

* 1 x nodemcu ESP8266 or Wemos D1 mini
* 3 x 47R resistor
* 1 x 470R resistor
* 3 x 2200R resistor
* jumpers or other ways of connecting to the GTR

### Preparing the ESP

Follow the instructions [here](stm32_esp.html).

### Connecting the ESP

The pinout for the GTR can be found [here](https://github.com/bigtreetech/BIGTREETECH-SKR-PRO-V1.1/blob/master/manual/SKR-PRO-V1.1-Pin.pdf) and the schematic for the Duet 2 Wifi for reference can be found [here](https://github.com/T3P3/Duet/blob/master/Duet2/Duet2v1.04/DuetWifiv1.04a_Schematic.pdf). 

The table below shows the pins required on the ESP8266 and what they are connected to on the SKR. Please ensure that your cables are no longer than 30cm although they should ideally be as short as possible.  

<div class="datatable-begin"></div>

| ESP Pin       | GTR Pin       | Resistor Value  |
| :-------------: |:-------------:| :---------------:|
| RST           | PB10 on EXP2         | 470R            |
| CS/GPIO15     | PB12 on EXP2         | 2200R           |
| MOSI/GPIO13   | PB15 on EXP2         | 47R             |
| MISO/GPIO12   | PB14 on EXP2         | 47R             |
| SCLK/GPIO14  | PB13 on EXP2         | 47R             |
| ESP_DATA_Ready/GPIO0   | PA15 on EXP1         | 2200R             |
| LPC_DATA_Ready/GPIO4   | PC11 on EXP1         | None            |
| VIN(5v)   | 5v on EXP1          | None             |
| GND   | GND on EXP1          | 2200R to RST             |

<div class="datatable-end"></div>

{% include warning.html content="The cables used need to be very very short. Even 10cm ones don't work so they must be shorter than that" %}

### Prepare the SD Card

Follow the instructions on [Getting Started with RRF3](getting_started.html)

### Board.txt file

You will also need a board.txt file in the sys folder. Below are the contents that should be used.

```
//Config for SKR GTR v1.0
board = biqugtr_1.0
8266wifi.espDataReadyPin = A.15
8266wifi.TfrReadyPin = C.11
8266wifi.espResetPin = B.10
heat.tempSensePins = { C.0, C.1, C.2, C.3, A.3, F.9, F.10, F.7, F.5 }
```

### Updating the ESP8266 by RRF

If you have a WiFi adapter that supports updating via RRF, you need to add the following information to the board.txt file.    
```
8266wifi.serialRxTxPins = { C.7, C.6 }
serial.aux.rxTxPins = { D.9, D.8}
```

### Smart Drivers

If using TMC5160 or TMC22XX drivers (where 22XX is either the TMC2208, TMC2209, TMC2225 or TMC2226), the following line must also be added to the board.txt file
```
stepper.numSmartDrivers = X
```
Where X is the number of drivers fitted in total.

#### TMC22XX UART Drivers

The drivers must be continuous and start at unit 0 (unless TMC5160 are also used, which case they must be installed after them). So, for the SKR board, if you have say 3 TMC2208s and 1 other driver, the 2208s must be in slots 0, 1, 2 and the remainiong driver in slot 3 or 4. You can use RRF to assign any of those slots to an axis/extruder. 

#### TMC5160 SPI Drivers

TMC5160 drivers are supported from 3.3 beta 1 onwards.
If using TMC5160 drivers, the following lines must also be added to the board.txt file.  
```
stepper.num5160Drivers = X
stepper.spiChannel = 5
SPI5.pins={B.3, B.6, G.15}
```
Where X is the number of 5160 drivers fitted. The drivers must be continuous and start at unit 0. So, if you have say 3 TMC5160s and 1 TMC22XX and 1 other driver, the 5160s must be in slots 0, 1, and 2, the TMC22XX in slot 3 and the remainiong driver in 4. You can use RRF to assign any of those slots to an axis/extruder. 

#### Sensorless Homing

**Supported by only the TMC2209, TMC2226 and TMC5160**
If using sensorless homing/stall detection with TMC2209 or TMC2226 the following line must be added to the board.txt file. It is not needed with TMC5160.
```
stepper.TmcDiagPins = {F.2, C.13, E.0, G.14, G.9, D.3, I.4, F.4, F.6, I.7, F.12}
```
Please only include the diag pin numbers where you intend to use sensorless homing on that axis.  
For example, if you only intend to use sensorless homing/stall detection on driver 0 and driver 1, only include F.2 and C.13 in your board.txt file.  
For more information about setting up sensorless homing, please read [this](sensorless.html).  

#### Driver Diag Pin

{% include warning.html content="The GTR sensorless homing jumpers don't do anything so if you don't want to use sensorless homing and use normal endstops, you will have to remove the diag pin from your drivers - [Evidence](https://github.com/bigtreetech/BIGTREETECH-GTR-V1.0/issues/12)" %}

### Board.txt Location

Place the *board.txt* file in a directory called "sys" on the SD card and install the SD card in the GTR.   

### Final Setup

Once connected, power up the board using 12-24v and connect to the USB port on the board. Using a program such as putty. Follow the instructions [here](putty.html) to set it up for RRF. Then type in the following  

```
M552 S0
M587 S"your SSID" P"your password"
M552 S1
```

{% include warning.html content="**DO NOT USE PRONTERFACE** it will convert all text to upper case. If you really must, please do the following. <br/>  If you wanted to use “PassWord”, you would write P”P’a’s’sW’o’r’d” with the ‘ indicating the following letter should be lower case. Explanation [here](https://duet3d.dozuki.com/Wiki/Gcode#Section_M587_Add_WiFi_host_network_to_remembered_list_or_list_remembered_networks)." %}

{% include important.html content="Both the SSID and Password used to connect to your WiFi are case sensitive."%}

The blue light on the wifi chip shoould then flash blue and will go solid when a connection has been established. The ip address will be shown on the serial connection. It is also possible to type just M552 to get the current ip address reported back.

The final thing to do is add the line “M552 S1” to your config file. This can be done through the web interface. This just ensures that the wifi connection is started at start up. There is no need to add the M587 command as this is written permanently to the flash of the ESP chip.  

### Once up and running

You will need to PID tune your tools and your bed. Please be aware that bed tuning may take up to an hour and tool tuning normally takes around 15 minutes. If it takes longer, that is also fine as up to 30 cycles may be ran.  

To tune the bed, run the following command, changing the temperature (the S value) if a different tuning temperature is required.  
```
M303 H0 S60
```  

To tune each tool, run the following command, changing the temperature (the S value) if a different tuning temperature is required. This proceedure will activate the part cooling fans during the final phase of the tuning process so their effect is taken into account. If your printer has more than one tool, make sure each one of them is tuned.  
```
M303 T0 S220
```

Once the tuning is complete, either copy the M307 command into the heater definitions or send M500, ensuring you have M501 at the end of your config.g.  
If the tuning fails at the end, carry on saving the values as in most cases the outputted values still work correctly.  
If the values still result in a heater fault, please refer to [this](https://duet3d.dozuki.com/Wiki/Tuning_the_heater_temperature_control#Section_Setting_the_model_parameters_manually) wiki page for information about how to adjust the values manually.  