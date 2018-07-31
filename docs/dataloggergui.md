---
layout: page
title: DataLogger GUI
---

# Data logger GUI

The data logger GUI is intended to contain all command line utilities for dealing with the data logger SD card and provide an interface for users to use. Users can check the SD card packets, extract the data, and convert it to a .rec file for further processing with Trodes and the [export functions](/doc/ExportFunctions).

**Windows startup**: Click yes on the popup requesting for administrative privileges. Those are needed to detect and read from SD cards.

**Linux startup**: Open via terminal with sudo, like so: `sudo ./DataLoggerGUI`.

## SD Card utilities

### Card Enable

CardEnable turns on recording for this SD card. This is required to be set manually before every recording session to make sure the user has extracted the data from the previous recording.

### PCheck

Packet check (PCheck) goes over all packets in the SD card without extracting it and prints a summary of the data.

### Write Config

Write config is a utility for more advanced users. Provided a .cfg file, writeConfig modifies the recording configuration the data logger uses on the SD card. Only use if you know what you are doing.

### Extract

This extracts all data from the SD card onto a .dat file. Use the "Browse" button to select the file name to save to, and click "Run SD Card Extract" button. A window pops up showing the progress of the extract script. When finished, the results panel will be green and display the outputted file.

## .rec Conversion

### .rec Files

.rec files are recordings that are playable by Trodes and used by the rest of the pipeline. In order to use the recording taken by the logger on the SD card, it must be converted into a .rec file. Users that also have DIO data from the MCU/ECU devices can choose to merge that .rec file with the logger's .dat file to create one unified .rec file.

### Without MCU

If you are doing a conversion from the SD card data straight to a .rec file

1. Select the .dat file (preloaded if you just extracted it)
2. Ignore the second option for a MCU .rec
3. Select a .trodesconf file to be used for the final .rec file
4. Choose the output file name and directory with "Browse" button
5. Click on the smaller "SD to Rec" button

### With MCU

If you are merging in a MCU .rec file with the data logger data file

1. Select the .dat file (preloaded if you just extracted it)
2. Select the MCU .rec file
3. Select a .trodesconf file to be used for the final .rec file
4. Choose the output file name and directory with "Browse" button
5. Click on the larger "Merge .rec and .dat" button
