---
layout: page
title: Export functions
---
- [Export Functions](#export-functions)
    - [Importing to Matlab](#importing-to-matlab)
    - [Importing to Python](#importing-to-python)
    - [Basic usage](#basic-usage)
- [More Options](#more-options)
- [Troubleshooting](#troubleshooting)
    - [Error with file: backwards timestamp](#error-with-file-backwards-timestamp)
- [Export functions available](#export-functions-available)
- [More info on export functions](#more-info-on-export-functions)
    - [exportanalog (analog I/O channels from ECU, etc)](#exportanalog-analog-io-channels-from-ecu-etc)
    - [exportdio (digital events)](#exportdio-digital-events)
    - [exportLFP (filtered LFP or raw unfiltered data)](#exportlfp-filtered-lfp-or-raw-unfiltered-data)
    - [exportmda (MountainLab)](#exportmda-mountainlab)
    - [exportofflinesorter (Offline Sorter)](#exportofflinesorter-offline-sorter)
    - [exportphy (Klusta-Suite) Phy format description](#exportphy-klusta-suite-phy-format-description)
    - [exportspikes (Spike snippets for MatClust, MClust, or general purpose spike extraction)](#exportspikes-spike-snippets-for-matclust-mclust-or-general-purpose-spike-extraction)
    - [exporttime (continuous time periods)](#exporttime-continuous-time-periods)

## Export Functions

Export functions are utilities that will extract and save specific features from a raw recording file (.rec) into a binary file. 

These features are meant to be used in terminal with arguments passed to specify various parameters. They can also be called from other scripting languages ('!' character in Matlab is used for this). See extractSpikeBinaryFiles.m (in TrodesToMatlab toolbox) for an example of how to wrap export functions inside Matlab.

### Importing to Matlab

TrodesToMatlab toolbox can be [downloaded here](https://bitbucket.org/mkarlsso/trodes/downloads/).

All extracted binary files can be read into Matlab using a single function: readTrodesExtractedDataFile.m (in the TrodesToMatlab toolbox).  This function will read the file's header info and data into a Matlab structure.

### Importing to Python

TrodesToPython toolbox can be [downloaded here](https://bitbucket.org/mkarlsso/trodes/downloads/).

All extracted binary files can be read into Python using a single function: readTrodesExtractedDataFile.py (in the TrodesToPython toolbox).  This function will read the file's header info and data. 

### Basic usage

```bash
#!sh

./exportLFP -rec /path/to/recording.rec -OPTION1 VALUE1 -OPTION2 VALUE2 ...
```

Following the executable, a recording (.rec) file needs to be passed with the -rec option. 

## More Options

By default, the export functions will use the settings defined when recording the data. But you can override these via command line options. Some export functions will different defaults, and can be seen by typing -h. 

All export functions will output a version of the following with the -h argument.
 
The *Defaults* displays any default values that will override the settings in the workspace and arguments passed. 

The *Additional options* section displays any extra options this specific export function will provide. 

```text
./exportLFP -h

Used to extract LFP data from a raw rec file and save to individual files. 
Usage:  exportLFP -rec INPUTFILENAME OPTION1 VALUE1 OPTION2 VALUE2 ...  

Input arguments 
Defaults:
    -outputrate 1500 
    -highpass 0 
    -lowpass 400 
    -interp -1 (inf) 
    -userefs 0 

Additional options: 
-oneperntrode <0 or 1>          -- if 1 (default) only the LFP channel is used for each ntrode. Otherwise all channels are exported.

Input arguments

-rec <filename>                 -- Recording filename. Required. Muliple -rec <filename> entries can be used to append data in output.
-output <basename>              -- The base name for the output files. If not specified, the base name of the first .rec file is used. 
-outputdirectory <directory>    -- A root directory to extract output files to (default is directory of .rec file). 
-reconfig <filename>            -- Use a different workspace than the one embedded in the recording file. 
-v or -version                  -- Prints the current executable's version information to the terminal. 

By default, the following options will use the settings in the workspace, unless specified above.
-highpass <integer>             -- High pass filter value. Overrides settings in file. 
-lowpass <integer>              -- Low pass filter value. Overrides settings in file.
-outputrate <integer>           -- Define the output sampling rate in the output file(s).
-interp <integer>               -- Maximum number of dropped packets to interpolate. 
-userefs <1 or 0>               -- Whether or not to subtract digital references.
-usespikefilters <1 or 0>       -- Whether or not to apply the spike filter settings in the file. 
-abortbaddata <1 or 0>          -- Whether or not to abort export if data appears corrupted. 
-paddingbytes <integer>         -- Used to add extra bytes to the expected packet size if an override is required

```

## Troubleshooting

### Error with file: backwards timestamp

This message is displayed if the timestamp is not increasing correctly, and causes the export to abort. It can sometimes occur at the beginning of the file once (and is then not a problem). Rerun the export function with the -abortbaddata 0 flag.  If multiple warnings are displayed as the export progresses there may be some corruption in the file.

## Export functions available

All export functions have an identical 'Input arguments' section. To view the export functions' unique help outputs, see below. 

- **exportanalog** - Used to extract Auxilliary analog I/O channels 
- **exportdio**    - Used to extract digital I/O channels 
- **exportLFP**    - Used to extract LFP data or continuous unfiltered data
- **exportmda**    - Used to extract continuous data in MDA format (for MountainLab)
- **exportofflinesorter** - Used to extract continous data for Offline Sorter
- **exportphy**    - Used to extract continuous data in PHY format (for Klusta-Suite)
- **exportspikes** - Used to extract spike waveforms 
- **exporttime**   - Used to extract continuous time periods 

## More info on export functions

'Input arguments' sections are omitted here, since they are identical to the example above

### exportanalog (analog I/O channels from ECU, etc)

```text
Used to extract Auxilliary analog I/O channels from a raw rec file and save to individual files for each channel. 
Usage:  exportanalog -rec INPUTFILENAME OPTION1 VALUE1 OPTION2 VALUE2 ...  
```

### exportdio (digital events)

```text
Used to extract digital I/O channels from a raw rec file and save to individual files for each channel. 
Usage:  exportdio -rec INPUTFILENAME OPTION1 VALUE1 OPTION2 VALUE2 ...  


```

### exportLFP (filtered LFP or raw unfiltered data)

```text
Used to extract LFP data from a raw rec file and save to individual files. 
Usage:  exportLFP -rec INPUTFILENAME OPTION1 VALUE1 OPTION2 VALUE2 ...  

Input arguments 
Defaults:
    -outputrate 1500 
    -highpass 0 
    -lowpass 400 
    -interp -1 (inf) 
    -userefs 0 

Additional options: 
-oneperntrode <0 or 1>          -- if 1 (default) only the LFP channel is used for each ntrode. Otherwise all channels are exported.

NOTE: THIS FUNCTION IS ALSO USED TO EXPORT CONTINUOUS UNFILTERED RAW DATA. HERE IS IS HOW:

exportLFP -rec myFile.rec -usespikefilters 0 -lowpass -1 -highpass -1 -userefs 0 -outputrate 30000

```

### exportmda ([MountainLab](https://github.com/magland/mountainlab))

```text
Used to extract data from a raw rec file and save to a set of files in MDA format. 
NOTE: FILTERING IS TURNED OFF. 
Usage:  exportmda -rec INPUTFILENAME OPTION1 VALUE1 OPTION2 VALUE2 ...  

Input arguments 
Defaults:
    -outputrate -1 (full) 
    -usespikefilters 1 
    -interp -1 (inf) 
    -userefs 1 


```

### exportofflinesorter ([Offline Sorter](http://www.plexon.com/products/offline-sorter))

```text
Used to extract continous data from a raw rec file and save to binary file that can be imported with Offline Sorter. 
Usage:  exportofflinesorter -rec INPUTFILENAME OPTION1 VALUE1 OPTION2 VALUE2 ...  

Input arguments 
Defaults:
    -outputrate -1 (full) 
    -usespikefilters 1 (Filter the channels with the saved spike filters)
    -interp -1 (interpolation not supported, do not change) 
    -combinechannels 1 (combine neural channels into one file. Instead use one file per channel.) 
    -appendauxdata 0 (do not append DIO and analog data into the file)
    -userefs 1 (Use the digital reference channels designated in the file)

To import the binary file into Offline Sorter, from the main menu of Offline Sorter select File | Import | Binary File with Continuously Digitized Data. Choose the following settings:

Number of channels: the total  number of channels in the exported file 
Data offset: 0
Digitization freq: 30000
A/D Conversion resolution: 16
Maximum Voltage (mV): 6.39
Swap bytes: No

```

### exportphy ([Klusta-Suite](http://klusta.readthedocs.io/en/latest/)) [Phy format description](http://klusta.readthedocs.io/en/latest/sorting/)

```text
Used to extract data from a raw rec file and save to a file for PHY file format. 
Usage:  exportPHY -rec INPUTFILENAME OPTION1 VALUE1 OPTION2 VALUE2 ...  

Input arguments 
Defaults:
    -outputrate -1 (full) 
    -usespikefilters 1 
    -interp -1 (inf) 
    -userefs 1 


```

### exportspikes (Spike snippets for [MatClust](https://bitbucket.org/mkarlsso/matclust/overview), [MClust](http://redishlab.neuroscience.umn.edu/MClust/MClust.html), or general purpose spike extraction)

```text
Used to extract spike waveforms from a raw rec file and save to individual files for each nTrode. 
Usage:  exportspikes -rec INPUTFILENAME OPTION1 VALUE1 OPTION2 VALUE2 ...  

Defaults:
     -invert 1 
    -outputrate -1 (full sampling, can't be changed') 
    -usespikefilters 1 
    -interp 0 
    -userefs 1 

Additional options: 
-invert <1 or 0>                -- Whether or not to invert spikes to go upward

NOTE 1: For MCLUST import of the extracted spikes files, use mClustTrodesLoadingEngine.m (in the TrodesToMatlab toolbox).

NOTE 2: For MatClust import, run createMatclustFile.m (in TrodesToMatlab/TrodesToNeuroQuery folder) from Matlab using an extracted spikes file as input.
```

### exporttime (continuous time periods)

```text
Used to extract continuous time periods from a raw rec file and save to binary file. 
Usage:  exporttime -rec INPUTFILENAME OPTION1 VALUE1 OPTION2 VALUE2 ...  

Defaults:
    -outputrate -1 (full sampling, can't be changed') 
    -interp 500 (used to define breaks in continuous periods) 
```