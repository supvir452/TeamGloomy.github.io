---
title: Firmware Building Instructions
tags: []
keywords: 
last_updated: 25/01/2021
summary: "Firmware Building Instructions"
sidebar: mydoc_sidebar
permalink: firmware_build.html
folder: mydoc
comments: false
toc: false
---

This page covers how to compile the firmware. These instructions are based on the new 3.2 unified firmware.

## Preparation of software

1. Download and install [VSCode](https://code.visualstudio.com/).  
2. Open VSCode and open the extension tab (this can be done by using the shortcut Ctrl+Shift+X) and search for windows-arm-none-eabi. Install the version by metalcode-eu.  
3. Download and install [git](https://git-scm.com/downloads).  
4. Then download and install [make](http://gnuwin32.sourceforge.net/packages/make.htm). Use the "complete package except sources" version. Once installed you will need to add the location of the bin folder that is installed in to your windows path environment setting. Make sure you add them to the system variables and not the user varaibles. While there also add the location of mkdir.exe, which will have been installed with git. It can be found in the usr/bin folder where git is installed. Once thats done, close VSCode and then reopen it.  
5. Close VSCode
6. Download and install latest [GNU Arm Embedded Toolchain](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads). Tick the box at the end to install it to your PATH.  

## Preparation of firmware code

Download the following github repositories. The preferred method for doing so is by using a git client (terminal or GUI). This allows for changes made to be tracked. My client of choice (jay_s_uk) is [gitkracken](https://www.gitkraken.com/).  
- [RRFBuild](https://github.com/gloomyandy/RRFBuild) - The main project for building
- [RepRapFirmware](https://github.com/gloomyandy/RepRapFirmware/tree/v3.02-dev-unified) - the main RRF source code 
- [RRFLibraries](https://github.com/gloomyandy/RRFLibraries/tree/v3.02-dev-unified) - Support libraries for RRF 
- [CoreLPC](https://github.com/gloomyandy/CoreLPC/tree/v3.02-dev-unified) - MCU specific support for LPC builds 
- [CoreSTM32F4](https://github.com/gloomyandy/CoreSTM32F4) - MCU specific support for STM32F4 builds 
- [FreeRTOS](https://github.com/gloomyandy/FreeRTOS) - RTOS support package 
- [DuetWifiSocketServer](https://github.com/gloomyandy/DuetWiFiSocketServer) - WiFi interface 

Clone all of the repositories into the RRFBuild folder.  
The resulting structure should look like this.  
![image](https://i.ibb.co/94bTCMd/build-structure.png)

## Building the firmware

Open VSCode.  
Go file -> open folder and browse to the RRFBuild folder.  
Once opened, the firmware can be built using terminal -> run build task and picking the type of build you want to make.