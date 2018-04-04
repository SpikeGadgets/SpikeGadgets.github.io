[Wiki Home](Home) > [User Manual](Documentation) > [Creating a Workspace](Workspace) > [Configuration](Configuration)

**Table of Contents**

[TOC]

-----------

#General Info

Workspace Configurations files are XML formatted files used to store Trodes system and hardware settings.  When you save a workspace, the file created is automatically given the extension '.trodesconf', but theoretically any xml formatted file can be loaded if written in the proper fashion.  Furthermore, editing .trodesconf files outside of can be done in the text editor of your choice.

Generally speaking, the configuration files are broken up into several different configuration sections.  That is, each configuration characterized below has it's own section within which multiple other configuration sections can be defined.  For example, look at this snippet taken from one of our sample workspaces (32_Singles_ECU.trodesconf):

       <GlobalConfiguration realtimeMode="1" filePath="" filePrefix="myAnimal" saveDisplayedChanOnly="1"/>
       <ModuleConfiguration>
          <SingleModuleConfiguration sendNetworkInfo="1" sendTrodesConfig="1" moduleName="CameraModule">
             <Argument flag="-camera" value="usb"/>
             <Argument flag="-resolutionX" value="1280"/>
             <Argument flag="-resolutionY" value="720"/>
          </SingleModuleConfiguration> 
          <SingleModuleConfiguration moduleName="SimpleCommunicator" sendNetworkInfo="1" sendTrodesConfig="1"/>
       </ModuleConfiguration>
       
In this example, there are two configuration sub-sections defined, the [global configuration](#markdown-header-global-configuration) and the [module configuration](#markdown-header-module-configuration).  The global configuration has no subsections, however, the module configuration has a sub section for each module it defines -- one for both Camera Module and Simple Communicator.  Furthermore, Camera Module's subsection has it's own subsection argument sections.  This subsection division structure allows us to separate different configuration groups out in a straight-forward and simple matter, making .trodesconf files easy to parse in third party programs such as Matlab.  For more information about each configuration subsection and their member flags/variables, please see the section below.


-----------

#Configuration Flags
* [Global Configuration](#markdown-header-global-configuration)
* [Hardware Configuration](#markdown-header-hardware-configuration)
* [Aux Display Configuration](#markdown-header-aux-display-configuration)
* [Spike Configuration](#markdown-header-spike-configuration)
* [Module Configuration](#markdown-header-module-configuration)

###Global Configuration

The Global Configuration's flags define general system information as well as many of the variables used for exporting recorded data.

Global Configuration Flags (header "GlobalConfiguration"):

| Flag | Description |
| --- | --- |
| compileDate | (**string**) String that contains the date when the Trodes program that saved the workspace file was compiled. |
| commitHead | **(string)** String containing the git commit head with which the Trodes program that saved the workspace file was compiled. |
| compileTime | **(string)** String containing the time of day at which the Trodes program that saved the workspace file was created. |
| fileChunkSize | **(int)** The maximum size of a recording file before a new one is started.  If set to -1, no chunking occurs and the file has no size limit. |
| filePath | **(string)** Default save file location.  This is where any saved .rec files will be automatically put. |
| filePrefix | **(string)** String that will be automatically appended to the front of any saved .rec files. |
| qtVersion | **(string)** Qt version with which the Trodes program that saved the workspace was compiled with. |
| realTimeMode | **(bool)** Specify whether to start Trodes in real time mode or not.  Setting this option to true spreads channels across multiple threads for closed loop, low latency data acquisition/visualization.  Enabling this option will greatly increase CPU demand. |
| saveDisplayedChanOnly | **(bool)** Specify whether to save hardware channel data for all channels or just those currently assigned to nTrodes.  More specifically, enabling this option saves channel data only for hardware channels displayed/assigned to specific nTrodes. |
| suppressModuleAbsPathWarning | **(bool)** Specify whether to suppress module absolute path warnings.  These warnings are thrown when a workspace opens a module using its' absolute system path rather than a relative path. |
| systemTimeAtCreation | **(var)** The system time that the workspace was created at. |
| timestampAtCreation | **(var)** The Trodes hardware timestamp when the workpace was created. |
| trodesVersion | **(string)** String containing the current Trodes version that the workspace was created with. |


###Hardware Configuration

The Hardware Configuration flags define general hardware settings not unique to connected devices.

Hardware Configuration Flags (header 'HardwareConfiguration'):

| Flag | Description |
| --- | --- |
| numChannels | (**int**) The number of hardware channels included in the current configuration (should be in intervals of 32). |
| samplingRate | (**int**) The frequency sampling rate (Hz).  By default this value is 30,000 and should not be changed unless your specific hardware is clocked at a different sampling rate. |

Additionally, the Hardware Configuration header also contains a set of subflags defining each attached device.  Note that __you should not change the values for any of the device flags unless you either know what you are doing or are creating a new device.__  All device configurations are loaded directely from the 'DeviceProfiles.xml' file, located in the Resources folder.

Device Flags (header "Device):  

| Flag | Description |
| --- | --- |
| available | (**var**) description |
| name | (**string**) The name of the device. |
| numBytes | (**var**) description |
| packetOrderPreference | (**var**) description |

Along with these, each device has a series of subflags defining each input/output channel.  Again, do not change these manually unless you know what you are doing.

Device Channel Subflags (header "Channel"):

| Flag | Description |
| --- | --- |
| bit | (**var**) description |
| dataType | (**string**) What kind of data is being transmitted over this channel.  Can either be 'digitial' or 'analog.' |
| id | (**string**) Identification string, or name, of each channel. |
| input | (**bool**) Boolean describing whether or not a channel is an input (true) or output (false) channel. |
| startByte | (**var**) description |


###Aux Display Configuration

While there are no flags attached directly to the Auxiliary Display Configuration (header 'AuxDisplayConfiguration'), there are a set of subflags for each auxiliary channel displayed.

Display Channel Flags (header 'DispChannel'):

| Flag | Description |
| --- | --- |
| analyze | (**bool**) Boolean that determines whether or not Trodes will keep track of state changes to this channel.  This is required for PETH plotting and for sending state changes to other modules. |
| color | (**hex**) Color hexadecimal value of the displayed channel's color. |
| device | (**string**) Name of the displayed channels parent device. |
| id | (**string**) Name of the channel being displayed. |
| maxDisp | (**int**) The maximum voltage range that this channel will display. For digital channels this value should nominally be 2, however, for analog channels this value can be up to the upper limit of 32,767 (very unlikely). |


###Spike Configuration

Like the Auxiliary Display Configuration, the Spike Configuration (header 'SpikeConfiguration') does not have any directly attached flags.  Instead, it has a series of subflags for each nTrode.

NTrode Flags (header 'SpikeNTrode'):

| Flag | Description |
| --- | --- |
| color | (**hex**) Color hexadecimal value of the nTrode's child channel's colors.|
| filterOn | (**bool**) Boolean that turns the Spike filter on or off. |
| highFilter | (**int**) The Spike filter's upper frequency bound.  If a 'spike' happens above this threshold, it will be filtered. |
| id | (**int**) The nTrode's identification number. |
| LFPChan | (**int**) This field specifies which channel (in relative 1-based indexing) in the nTrode will be used for measuring the Local Field Potential (LFP). |
| LFPHighFilter | (**int**) The upper bound for the LFP low-pass filter|
| lowFilter | (**int**) The Spike filter's lower frequency bound.  If a 'spike' happens below this threshold, it will be filtered. |
| moduleDataOn | (**bool**) description |
| refChan | (**int**) A one-based index of the digitally referenced nTrode channel.  Do not change this value manually, it is calculated by Trodes automatically. |
| refNTrode | (**int**) A one-based index of the digitally referenced nTrode.  This value allows for high-speed access of digital reference channels.  Do not change this value manually, it is calculated by Trodes automatically.|
| refNTrodeID | (**int**) The identification number of the digitally referenced nTrode.  This value can be safely changed manually as long as the referenced nTrode exists. |
| refOn | (**bool**) Turn on digital referencing for this nTrode. |
| tags | (**string**) A string containing this nTrode's grouping tags, each concatenated by a ';' |

Additionally, each nTrode has a subset of hardware channel flags attached to it.

Hardware Channel Flags (header 'SpikeChannel'):

| Flag | Description |
| --- | --- |
| hwChan | (**int**) The hardware channel's identification number. |
| maxDisp | (**int**) The spike display range (+/-) in micro volts. |
| thresh | (**int**) The spike trigger's threshold in micro volts. |
| triggerOn | (**bool**) Specify whether or not to run on spike triggering for this particular hardware channel. |


###Module Configuration

Module Configuration Flags (header 'SingleModuleConfiguration):

| Flag | Description |
| --- | --- |
| moduleName | (**string**) The module's name.  Note that this string is used to open the module in question, so it must match the name of the module's executable. |
| sendNetworkInfo | (**bool**) This boolean determines whehter or not Trodes will send the server address to the module on startup.  By default this value is set to true (1). |
| sendTrodesConfig | (**bool**) This boolean determines whether or not Trodes will send the configuration file location to the module on startup, allowing the module to read in specific command line arguments from the configuration file.  By default this value is set to true (1)|


Additionally, each module can have a set of argument subflags.

Module Arugment Flags (header 'Argument'):

| Flag | Description |
| --- | --- |
| flag | (**string**) String containing a module-specific command line argument flag. |
| value | (**string**) String containing a module-specific command line argument value. |