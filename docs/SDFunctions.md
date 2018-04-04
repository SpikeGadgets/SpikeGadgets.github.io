[Wiki Home](Home) > [User Manual](Documentation) > [Exporting Data](Export) > [SD Card Data](SDFunctions)

**Table of Contents**

[TOC]

#Using SD Card data

##[Recording with SD cards](http://spikegadgets.com/downloads/SD%20Instructions.pdf)
For detailed instructions on recording to SD cards with Datalogger, follow the [link above](http://spikegadgets.com/downloads/SD%20Instructions.pdf). 

##Converting SD recording to .rec file

Once you have extracted the temporary .dat file from the SD card, you will need to convert the file into a
format that is used by the Trodes suite. This means that a proper header needs to be appended to the
beginning of the file the contains information about the contents. There are two ways to do this.

1. If you do not need to combine the data with a simultaneous environmental recording from the MCU and
ECU, then the helper program (included in the Trodes software suite) called “sdtorec” will simply add the
desired workspace info to the output file. From the command line, use the following syntax:

    ```
    sdtorec -sd [SDRECORDING.dat] -numchan [NUMCHANNELS] -mergeconf [WORKSPACE.trodesconf]
    ```
    
    **Here is an example for the following setup:**

    - 32 channel headstage with 9-axis sensor included
    - No ECU or MCU data to merge with SD log

    ```
    sdtorec -sd /PATH/TO/RECORDING/FILENAME.dat -numchan 32 -mergeconf PATH/TO/WORKSPACES/32_Singles_NoECU_Sensors.trodesconf
    ```

2. If you need to combine the data with a simultaneous environmental recording from the MCU and ECU,
then the helper program (included in the Trodes software suite) called “mergesdrecording” will merge the
neural recording from the SD card with the environmental recording taken using Trodes. From the
command line, use the following syntax:

    ```
    mergesdrecording -rec [ENVIRONMENTALRECORDING.rec] -sd [SDRECORDING] -numchan
    [NUMCHANNELS] -mergeconf [WORKSPACE.trodesconf]
    ```

    **Here is an example for the following setup:**

    - 128 channel headstage with 9-axis sensor and RF sync receiver included
    - ECU and MCU data to merge with SD log

    ```
    PATH/TO/TRODES/mergesdrecording -rec PATH/TO/ECU/RECORDING/myRecording.rec -sd PATH/TO/RECORDING/sdFile.dat -numchan 128 -mergeconf 128_Tetrodes_ECU_Sensors.trodesconf
    ```

##Troubleshooting

**Error with file: backwards timestamp**

This message is displayed if the timestamp is not increasing correctly, and causes the export to abort. It can sometimes occur at the beginning of the file once (and is then not a problem). Rerun the export function with the -abortbaddata 0 flag.  If multiple warnings are displayed as the export progresses there may be some corruption in the file.