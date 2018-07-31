---
layout: page
title: Workspace/Configuration Files
---
- [What is a Workspace](#what-is-a-workspace)
- [Creating a Workspace Configuration](#creating-a-workspace-configuration)
    - [Method 1: Create a workspace using Trodes](#method-1-create-a-workspace-using-trodes)
- [Method 2: Create a workspace using the standalone WorkspaceEditor.](#method-2-create-a-workspace-using-the-standalone-workspaceeditor)
- [Method 3: Create a workspace by manually](#method-3-create-a-workspace-by-manually)
- [Using the Workspace Editor](#using-the-workspace-editor)
    - [General Settings Tab](#general-settings-tab)
    - [Global Configuration Group](#global-configuration-group)
    - [Hardware Configuration Group](#hardware-configuration-group)
    - [Stream Display Group](#stream-display-group)
- [Auxiliary Configuration Tab](#auxiliary-configuration-tab)
    - [Auxiliary Channel Settings](#auxiliary-channel-settings)
- [Trode Configuration Tab](#trode-configuration-tab)
    - [nTrode Settings Panel](#ntrode-settings-panel)
    - [nTrode Filters Panel](#ntrode-filters-panel)
- [Module Configuration Tab](#module-configuration-tab)
    - [Add Module Panel](#add-module-panel)

## What is a Workspace

A workspace is the term we use to describe an experiment configuration file (extension: .trodesconf).  Workspaces store both hardware and software configuration information, basically telling Trodes what devices it is connected to, what data to expect from those devices, and how to format the incoming data.  As such, workspaces are required for experiments to be run on Trodes.  However, more than that, they also provide a way for those same experiments to be both easily replicated by other users and quickly reopened in a separate session by the original creator.

---------------------------

## Creating a Workspace Configuration

### Method 1: Create a workspace using Trodes

The first way you can create a workspace configuration file is through the main Trodes program.  This method is recommended for creating new workspaces as you can immediately use them after creating them. 

1. To begin, open Trodes and click on the menu item labeled "Create Workspace."
2. In the submenu that opens you have two options, "From Scratch" and "Edit Existing File."
    * Selecting the "From Scratch" option will open the workspace editor menu of a blank workspace with a few defaults selected.
    * Selecting the "Edit Existing File" will open the workspace editor and populate it with a user-selected workspace's configuration settings. 
3. Tweak and edit all workspace settings as you see fit via the menu options.  See the [using the workspace editor section](#using-the-workspace-editor) for information on what the different options in the editor menu do.
4. When you are finished editing, click the accept option.  If you made any mistakes or configured your workspace incorrectly, an error message will appear informing you what settings you need to correct.  Assuming you've done that, hitting 'accept' again will open the workspace in the Trodes main program.
    * __NOTE:__ Hitting 'accept' will save the workspace as "tempWorkspace.trodesconf" in the same directory as your Trodes executable.  If you want to save the workspace for future use, make sure to either manually rename the file or save the file in the Trodes main program via the File->Workspace->Save As menu option.

## Method 2: Create a workspace using the standalone WorkspaceEditor.

Another good way of creating or editing a workspace is by using the standalone WorkspaceEditor module.  This module contains the same editor menu as one embedded in the Trodes main program, but with the major difference that you cannot load a newly configured workspace into Trodes main directly from the WorkspaceEditor module.  However, if you simply want to edit or configure a workspace without using it, this method is recommended.

1. Open the WorkspaceEditor program and select either the "Load" or "New" options to edit an existing workspace or create one from scratch respectively.
2. Edit all workspace settings until you are satisfied with your configuration, and save the workspace using the 'Save/Load' menu.  Again, like in Method 1, the workspace editor will prompt you if you have accidentally made an invalid workspace.
3. You can load another workspace at any time through the 'Save/Load' menu.

## Method 3: Create a workspace by manually

The last way you can create or edit a workspace is by using a text editor program (gedit, notepad++, etc...) to create/edit a .trodesconf file manually.  However, this method is generally not recommended for anyone except advanced users as manual editing is both time consuming and error prone.  If you do decide to edit a workspace file manually, there are a few things you should be aware of:

* .trodesconf files are formated as xml files, so standard xml syntax applies.
* Generally speaking, the device profiles should not be changed at all unless you are using non-SpikeGadgets hardware.
* Please refer to the [configuration documentation](Configuration) for information on what all the different flags are and how you should edit them.
* Several of the configuration flags' values are relative based on what else is in the configuration file.  The "refNtrode" flag, for example, gives the location of the a referenced nTrode based on that nTrode's configuration order in the workspace.  If you change the order of nTrodes in your configuration file (or add/remove one), then you will have to manually update the references on all affected nTrodes.

As a final note, if you are experiencing crashes or other issues while using a manually configured workspace, chances are the issue is coming from an error in the workspace file.  It is for this reason that the first two methods are highly recommended over this method.

---------------------------

## Using the Workspace Editor

The Workspace Editor is a graphical user interface that provides a convenient way for users to create and edit Trodes configuration files without the hassle of having to mess with the underlying xml.  Available in both a standalone module and embedded in Trodes, the Workspace Editor is a dialog menu made up of several tabs; a [general settings tab](#general-settings-tab), a [auxiliary configuration tab](#auxiliary-configuration-tab), a [trode configuration tab](#trode-configuration-tab), and a [module configuration tab](#module-configuration-tab).

### General Settings Tab

The General Settings tab allows the user to edit the Global Configuration, the Hardware Configuration, and the Stream Display Settings.  As such, each setting group is organized into different groupings.

### Global Configuration Group

| Option | What it does |
| --- | --- |
| Enable Real Time Mode | Clickable check box that allows you to enable or disable real time mode.  Enabling real time mode spreads channels across multiple threads for closed loop, low latency data acquisition/visualization and greatly increases CPU demand. |
| Save Displayed Channel Only | Clickable check box that allows you to specify whether to save hardware channel data for all channels or just those currently channels displayed/assigned to specific nTrodes. |
| Set File Path | Line editor that, when clicked, will prompt you to select a default save directory.  This directory is where all .rec files will be deposited. |
| Set File Prefix | Line editor that allows you to give saved .rec files a default prefix string.  In other words, anything entered into this box will be added to the front of the .rec file's name. |

### Hardware Configuration Group

| Option | What it does |
| --- | --- |
| Channel Picker | Drop down menu that allows you to select the number of hardware channels you are using.  You can select any channel configuration between 0-1024 in intervals of 32.  __NOTE:__ Setting the channel number to 0 will initialize Trodes with no stream display, only auxiliary channels will be visible. |
| Hardware Sampling Rate | Text editor that allows you to set the hardware sampling rate frequency.  This is set to 30,000 Hz by default.  __It is highly recommended that you do not change this unless you have hardware specifically keyed for different frequency.__ |

The Hardware Configuration Group also contains an embedded Device Panel that may be used to add or remove different devices to your workspace.  To add a device, simply click the "Select Device" option, select the desired device from the drop down menu, and then click the "+Add Device" button.  The newly added device will be added to the list of "Current Devices."  To remove a device, select one of the devices from the "Current Devices" list and click the "Remove" button.  __NOTE:__ All workspaces must have a SpikeGadgets Main Control Unit (MCU_IO).

Currently, the devices you can add/remove from your workspace are:

| Device | Description |
| --- | --- |
| MCU_IO | The SpikeGadgets Main Control Unit.  Has 8 configurable digital input channels (see the Auxiliary Configuration for more details)
| ECU | The SpikeGadgets Environmental Control Unit.  Has 32 configurable digital input channels, 32 configurable digital output channels, 8 configurable analog channels, and 8 configurable analog output channels.
| headstageSensor | A SpikeGadgets headstage sensor.  Has 3 xyz analog outputs for accelerometer, gyroscopic, and magnetometer data |
| RF | A wireless transceiver that can be plugged into the MCU.  Has an RFsync analog output channel. |

### Stream Display Group

The Stream Display Group panel allows you to edit the basic settings for how Trodes displays the hardware channel traces (this is the main central display widget you see when loading a workspace).  

| Option | What it does |
| --- | --- | 
| Display Columns | Pull down menu that allows you to select how many columns you want the stream display to use when displaying nTrode channel traces.  Currently you may only select 1-4, and furthermore you must have more nTrodes than display columns (for example, selecting 2 columns when having only 1 nTrode will cause an error).
| Display Pages | Pull down menu that allows you to select how many tabs you would like the stream display to distribute configured nTrodes/channels accross.  You may currently only select 1-4 pages. |
| Background Color | Pressing the colored box will bring up a color picker menu that allows you to select what background color you would like to have for the stream display.  It is recommended that you choose either a dark or light basic color (grey, black, etc...) to contrast with each channel's color. |

## Auxiliary Configuration Tab

The Auxiliary Configuration Tab allows you to selectively add/remove/edit different available device hardware channels (referred to as 'aux channels') to your configuration from a list of available devices previously determined in the General Settings Tab.  The Auxiliary Configuration Tab consists of two main sections; in the top half is a Auxiliary Channel Settings Group which allows you to edit individual aux channel settings, and the bottom half which features two list views, the left being a list of added/configured aux channels and the right a list of available channels to add.  

To add an aux channel, select a channel from the right list, and simply click the "+Add" button.  Then to edit the added channel's settings, select the channel in the left list view.  This will load the channels settings into the Auxiliary Channel Settings panel, allowing you to manipulate the fields as you see fit.  All changed settings are automatically saved to the selected nTrode(s).  Clicking "Apply to selected" after selecting multiple channels (via shift-clicking) will apply the settings of the first selected channel to all selected channels.  Furthermore, the "Apply To All" button may be used to save the currently selected channel's settings to all other added aux channels.  

All added aux channels are visible in the main Trodes program in the "Aux" tab in the main stream display.

### Auxiliary Channel Settings

| Option | What it does |
| --- | --- |
| ID | __Not Editable__ Displays the identification string name of the currently selected channel. |
| Device | __Not Editable__ Displays the name of the currently selected channel's parent device. | 
| Maximum Display | Allows the user to select the maximum display resolution voltage of the selected aux channel.  The default value for digital channels is 2 and for analog channels, 2000.  It is not recommended to change the digital channel's value from the default. | 
| Channel Color | Displays the selected aux channel's trace color (the line representing its output in Aux tab of the stream display in Trodes).  Clicking this box will prompt you with a color picker dialog allowing you to select a new trace color. | 

## Trode Configuration Tab

The Trode Configuration Tab allows provides the controls necessary to create/remove nTrodes, add/remove channels to those nTrodes, and configure the settings for each individual nTrode.  The Trode Configuration Tab consists of three major sections; the nTrode Settings panel which allows you to edit the settings of selected nTrodes, the nTrode Filters panel which allows you to filter the displayed nTrodes based on their assigned grouping tags, and the two bottom list views -- the left displaying configured nTrodes and the right displaying unassigned hardware channels.

The primary purpose of the Trode Configuration Tab is to configure nTrodes.  You can add nTrodes using the nTrode Settings panel by pressing the "Add New" button.  You can configure the newly added nTrode's settings by manipulating the options in the panel either before or after you add it.  To add channels to the new nTrode, or any nTrode for that matter, simply drag and drop a hardware channel from the right list onto the nTrode in the left list that you want to assign it to.

Clicking on any nTrode or that nTrode's assigned channels in the left view will load the nTrode's settings into the nTrode Settings panel.  You can then edit that nTrodes setting by simply interacting with the panel's UI widgets (note that changed settings will be applied automatically).  Like with the aux channels, you can apply one nTrode's settings to several others by shift-clicking them and then clicking the "Apply to selected" button, or you can apply to all nTrodes using the "Apply to all" button. 

Pressing the delete/backspace key will remove any selected nTrode(s) and un-assign the associated hardware channels.

### nTrode Settings Panel

The nTrode Settings Panel is made up of 5 sub groups of settings.  

The first of those groups is the Digital Referencing Referencing Group:

| Option | What it does |
| --- | --- |
| Enable Digital Referencing | Clicking this checkbox enables digital referencing for the selected nTrode.  Defaults to off. |
| nTrode | Pull-down menu that allows you to select the ID of the nTrode you want the selected nTrode to reference. |
| Channel | Pull-down menu that allows you to select the specific channel that you want to reference. |

The second group is the Local Field Potential (LFP) Group:

| Option | What it does |
| --- | --- |
| Channel | Pull-down menu that allows you to choose which of the selected nTrode's channels you want to use for LFP. |
| High Cutoff | Pull-down menu that allows you to choose what you want the upper bound of the LFP low-pass filter to be.  Defaults to 100 Hz. |

The third group is the Spike Filter Group:

| Option | What it does |
| --- | --- |
| Enable Spike Filter | Clicking this checkbox enables spike filtering in the selected nTrode.  Defaults to on. |
| Low | Pull-down menu that allows you to choose the lower bound for the spike filter.  Defaults to 600 Hz. |
| High | Pull-down menu that allows you to choose the upper bound for the spike filter.  Defaults to 6000 Hz. |

The fourth group is the Spike Trigger Group: 

| Option | What it does |
| --- | --- |
| Enable Spike Trigger | Clicking this checkbox enables spike trigger detection for the selected nTrode.  Defaults to on. |
| Threshold | Allows you to specify the spike trigger threshold in micro volts.  Defaults to 10 μV. |
| Max Display | Allows you to specify the maximum display range in micro volts.  Defaults to +/- 50 μV. |

The last group is the Grouping Tags Group.  This group allows you to assign custom tags to the selected nTrodes.  There is no limit on the number of tags you can add to an nTrode, and you can add the same tag to multiple nTrodes.  By default, there are no tags assigned to any nTrodes, so you will have to create your own by selecting the "Select Tag to Add" menu, picking the "+Create New Tag" option.  Once you have assigned a new tag to an nTrode and applied the setting, that tag will be available to use on other nTrodes.

### nTrode Filters Panel

The nTrode Filters Panel allows you to filter the nTrodes visible in the left list based on their grouping tags.  For example, if you have two nTrodes, one with the tags "Hippocampus" and "Group 1", and the other with the tag "Group 1," selecting "Hippocampus" in the filters drop-down menu will cause only the first nTrode to be shown.  Likewise, selecting the "Group 1" tag will make both nTrodes visible in the list below. 

| Option | What it does |
| --- | --- |
| Filters | Pull-down menu that allows you to select which filter you want to use.  You can select multiple filters. |
| Filter Operation. | Allows you to apply a standard AND or an OR operation on the selected filters. |
| Clear Filters | Clicking this button will clear all filters. |

## Module Configuration Tab

The Module Configuration Tab provides an interface with which to add modules to your workspace.  The panel consists of two sections, the Add Module Panel and the Module List.  The Add Module Panel is used to add or edit modules while the Module List displays added modules and their settings. 

To add a module, select the drop down list labeled "Select Module" and select the module that you want to add.  More information about the modules that come prepackaged with Trodes can be found [here](Modules), however, if you want to add a different, non-standard module (for example, one you created yourself), you can add by selected the "+Add New" option.  Simply type in the name of your modules executable (on windows and linux) or app bundle (on mac), and click add. 

You can add command line arguments to a module by selecting it in the list view and clicking the "+Add Argument" button.  Add in the desired command line flag and value in the prompted dialog.

To edit/remove a module or a module argument, click the module or module argument in the list view and click either the "Edit" or "Delete" button.  When you click the "Edit" button on a module, the Add Module Panel load's the module's settings and allows you to edit them, make sure to click "Apply" when you are ready to save your changes.  Likewise, clicking the "Edit" button on an argument will bring up the argument dialog, allowing you to change the argument's flag and value.

### Add Module Panel

| Option | What it does |
| --- | --- |
| Select Module | Drop down menu that allows you to select a pre-defined module, or add a user defined one. |
| Send Network Info | Checkbox that tells Trodes whether or not to send the Trodes network server to the module on startup.  This is enabled by default. |
| Send Trodes Config | Checkbox that tells Trodes whether or not to send the configuration file path to the module on startup.  This is enabled by default. |
| Add/Apply Button | Button that either adds a module to the module list or applies changes to an existing module's settings. |