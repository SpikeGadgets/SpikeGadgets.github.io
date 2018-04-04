---
layout: page
title: Getting Started
---
# Table of Contents

- [Getting Started](#getting-started)
    - [Creating/Opening a Workspace](#creatingopening-a-workspace)
    - [Opening a Playback File](#opening-a-playback-file)
    - [Streaming Data from a Source](#streaming-data-from-a-source)
    - [Basic controls](#basic-controls)

## Getting Started

Upon opening Trodes you will be greeted with an intro page that will allow you to help you navigate the program.  From this menu, you can create a workspace, open a pre-existing workspace, or open a playback file.

### Creating/Opening a Workspace

In it's most basic form, Trodes is run by opening a workspace file (extension .trodesconf).  These files are used by Trodes to save experimental configuration settings, including what kind of hardware will be streaming data from and how that data will be displayed in Trodes.

To open a workspace, simply press the "Open Workspace" button and select the "Browse" option, or click "ctrl+o", and open the desired workspace file.

If you do not already have a workspace file you want to use, you can create you own by following the instructions outlined in the [Working with Workspaces](Workspace) section of the wiki.

### Opening a Playback File

If you have a data recording (extension .rec) file that you want to open simply click the "Open Playback File" button and select the "Browse" option, or click "ctrl+p" and open the desired playback file.  Once open, click the green play button to begin playback.

### Streaming Data from a Source

Once you have opened a workspace file, you are now ready to stream data.  Normally, you would be streaming data directly from your hardware setup,.  However, if for some reason you do not have your hardware connected to your computer, you can still test certain Trodes functions by simulating a data stream.  To do this, follow the below instructions:

1. Select "Connection->Source->Simulation->Signal generator"

   - You can also select "Connection->Source->Simulation->Signal generator (w/spikes) option"

2. Select "Connection->Stream from source" to start data streaming

3. Go to "Connection->Generator" controls to play with the input signal

If you want to stream data but do not want to use the signal generator, you can also stream data directly from your hardware setup via the following steps:

1. Select either "Connection->Source->SpikeGadgets->Ethernet" or "Connection->Source->SpikeGadgets->USB" depending on which port you will be streaming your data over

2. Select "Connection->Stream from source" to start data streaming

### Basic controls

1. If you click on a channel, it becomes highlighted.  The audio changes to that channel, and the nTrode that the channel belongs to becomes the selected nTrode.

2. There are buttons at the top of the window.  The ‘Spikes’ button will open up a spike trigger window.  When you click on a channel, the nTrode displayed will automatically update.

3. The ‘Video’ button links to the video module.  If you click on this button, and if you have a USB webcam or Allied Vision GIGE camera connected, a window will pop up showing live video.  When you save data, this module will save video, where the frames are time stamped with the same time values as the neural recording timestamps.This module can also do online and offline rodent position tracking.

4. The ‘nTrode’ button allows you to set critical values for the selected nTrode, such as a digital reference and filter values.

5. If you save data on to .rec file, that file can later be replayed using Connection->Source->File.

6. For some workspaces, you’ll notice that there is an ‘Aux’ tab displaying the auxiliary digital and analog lines configured in the workspace file.