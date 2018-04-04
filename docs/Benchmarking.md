[Wiki Home](Home) > [User Manual](Documentation) > [Benchmarking](Benchmarking)

# Contents

* [Enabling Benchmarking](#markdown-header-enabling-benchmarking)
    * [Benchmarking Control Panel](#markdown-header-benchmarking-control-panel)
    * [Command Line Call](#markdown-header-command-line-call)
    * [Workspace Configuration](#markdown-header-workspace-configuration)

--------------------

Sometimes it is useful for users to test the latency of Trodes on their system.  To do this, you can run Trodes in a 'Benchmarking Mode', and select a specific (or several) test to run.  Latency is tested using the computer's current system time in milliseconds, measured from the start of the day.

There are several different ways to run Trodes in its' 'Benchmarking Mode.'  The easiest of these is to simply access the Benchmarking Control Panel GUI via the Debug->Benchmarking menu option in the main Trodes window.  The other ways to initialize 'Benchmarking Mode' are through command line arguments and loading a .trodesconfig file with benchmarking settings enabled in it.  To avoid conflicts between these different methods of modifying benchmarking settings, settings modified by the user via the Benchmarking Control Panel override command line benchmarking arguments which override configuration settings.

__Note:__ All benchmarking test information is printed directly to the console that Trodes was called from.

--------------------

##Enabling Benchmarking

###Benchmarking Control Panel

You can enable benchmarking and further modify the benchmarking settings at runtime via the Benchmarking Control Panel menu in the Trodes main window.  The menu can be accessed by either accessing the Debug->Benchmarking option from the main menu bar or by pressing 'ctrl+b.'

The control panel menu is separated into three major latency groups; __Spike Latency, Position Latency, and Event System Latency__.  For each group there are a number of different tests that can be enabled or disabled, and each test's print delay (the amount of times it will skip printing the latency information to the console to prevent flooding the console).  By default, all tests are disabled, and the print delay values are set a value defined in configuration.h.  To enable any test, simply click the checkbox next to the name of the test you want to initialize, and edit the corresponding 'Print Delay' field to the desired value.  Apply your changes to Trodes by either pressing the 'Apply' button or the 'Enter' key.

The groups and their corresponding tests are as follows:

* __Spike Latency__
    * Available Tests
         * Spike Detect
              * Enables latency testing between when continuous data is received from hardware and when a spike is detected in the software.
         * Spike Send
              * Enables latency testing between when continuous data is received from hardware and when a full spike wave is sent to other modules
         * Spike Receive 
              * Enables latency testing between when continuous data is received from hardware and when a spike is received in another module.  This option currently only works for SimpleCommunicator.

* __Position Latency__
    * Available Tests
         * Position Streaming
              * Enables latency testing between when camera frames are received by CameraModule and when the data extracted from that frame is available in other modules.  Currently only works for SimpleCommunicator.

* __Event System Latency__
    * Available Tests
         * Event System
              * Enables latency testing between an event occurs and when it is received by listening modules.  Works for any module that has the event system initialized. 

__Note:__ The Benchmarking Control Panel will reflect benchmarking settings loaded from the command line or read in from a configuration file.

###Command Line Call

You can enable benchmarking from the command line by adding 'benchmarking' as an argument while calling the main Trodes program.  You can enable specific benchmarking tests with additional subflags.  The flags are as follows:

| Flag | Description |
| --- | --- | 
| recordSysTime | Trodes will record the system time each time it receives a new data point |
| spikeDetect | Trodes will print latency times between when continuous data is received from hardware and when a spike is detected.  Specifically, this is the latency between when the last datapoint in the spike is received from hardware and when that spike is fully written (all forty data points are processed) |
| spikeSent | Trodes will print latency times between when continuous data is received from hardware and when a full spike wave is sent to other modules. |
| spikeReceived | Trodes will print latency times between when continuous data is received from hardware and when a spike is received in another module.  This option currently only works for SimpleCommunicator. |
| positionStreaming | Trodes will print latency times between when camera frames are received by CameraModule and when the data extracted from that frame is available in other modules.  Currently only works for SimpleCommunicator. |
| eventSys | Trodes will print latency times between an event occurs and when it is received by listening modules.  Works for any module that has the event system initialized. |

The user can also follow the flag with an integer argument value to specify the print delays of the associated test.

__Note:__ these flags can be called in any order after the 'benchmarking' flag is given.  Further, inputing no integer value after a flag will set the print delay to the default values enumerated in the configuration.h file.

__Example:__

      ./Trodes benchmarking eventSys 10 spikeDetect
>Initializes event system and spike detection tests with specified print delays of 10 and the default respectively.

###Workspace Configuration

Benchmarking settings can be specified in .trodesconf files and be automatically loaded into Trodes when loading workspaces.  This is done in the same way that other configurations are called and using the same flag arguments that Command Line Benchmarking uses.  Adding the flag will initialize the correspond test, and adding a integer argument afterwards will set the print delay.

Example implementation in a .trodesconf file:

      <BenchmarkConfiguraion eventSys="1" spikeDetect="10" positionStreaming="32" />
      
>This code turns on event system, spike detection, and position streaming latency tests with print delays of 1, 10 and 32 respectively.