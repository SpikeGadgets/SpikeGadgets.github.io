[Wiki Home](Home) > [Release Notes](Release Notes)

# Release Notes

##1.7.2 (Mar 27, 2018)
```text
Release Notes for Trodes Suite version 1.7

Major Features

- NTrode selection and settings: Multiple ntrodes can now be selected, by using typical conventions with SHIFT or CTRL and clicking on multiple ntrodes. The ntrode button now creates an ntrode settings panel which applies the changes to all ntrodes selected. 

- Categories and tags for ntrodes: Ntrodes can be assigned categories and tags in the settings panel. Categories can have any number of tags. Any ntrode can be labeled with a category-tag combination. They are stored in the config file. The categories and tags can also be used to filter and select ntrodes. 

- Spike display: Unclustered points and waveforms can now be hidden. This is useful when clusters are created and the user wants to hide unclustered points. Both the scatterplot and waveforms plot support this. Individual clusters can also be shown or hidden at the menu from a right-click on a cluster. 

- Stream display: The user can decide to view a spike stream, LFP stream, or raw unfiltered stream. The LFP stream only displays the designated channel. The filters are separate from the stream display, so the user can view the LFP stream while still enabling the spike filter. 

- Hardware disconnects: if the hardware briefly disconnects, the system will automatically attempt to reconnect. There is also now an indicator for hardware status. 

- Moved to Qt5.9: Qt has fixed several of their own bugs and improved features over their releases. We are moving from 5.5.1 to 5.9.1. 



Bug fixes from 1.7.0-1.7.2

- Critical bug fix in export spikes.

- Opening a file on cameraModule on a different machine will result in creating a folder for the recording in the cameraModule's directory

- Switching cameras is now more stable and does not result in random crashes

- Monitor switching should no longer cause crashes, was reintroduced by new Qt version 5.9

- Fixed GUI on cameramodule that was ambiguous and did not prevent user error

- LFP and unfiltered data polarity was mistakenly reversed. They are now normal. Spike polarity is kept as reversed.

- Fixed disconnect fault tolerence with USB connection

- Export functions bugs and settings fixes

- Build script fixed on Linux
```

##1.6.1 (July 6, 2017)
```
#!text

MINOR CHANGES

-We are now a registered Apple developer . This means Mac users will not receive messages about Trodes being from an "unverified developer"

-Can now highlight and select multiple channels/ntrodes at a time in the workspace gui using shift click

-Workspace GUI has a more intuitive interface 

-CameraModule can now save and load inclusion/exclusion zone geometries

MINOR BUGFIXES

-Workspace GUI highlighting and selecting bug fixes.

-Available space in hard drive is back in status bar when recording

-CameraModule in playback mode opening in the wrong mode or the wrong files is fixed

-Changed video encoding so there isn't a missing frame at the end of a recorded video file

-Merge SD-Rec bugs fixed

```

##1.6.0 (June 7, 2017)
```
#!text
MAJOR FEATURES

—Multi waveform plotting.  The spike waveform viewer can not display many spikes at once and color them according to sorted spikes.  

—Workspace editor interface.  Workspaces can now be generated and edited using a graphical interface.  This eliminates the need to know XML to create or edit workspaces.

—File playback improvements.  File playback mode now uses a slider bar to allow the user to skip to any part of the file.  The camera module will open the accompanying video (if it exists) and synchronize playback with neural data.

—Intro menu.  Upon startup, Trodes now displays a menu allowing the user to quickly access previously used workspaces or recordings.

—Headstage settings dialog.  Users can now change headstage settings such as auto settle, smart referencing, and motion sensor on/off states from Trodes.

—Improved recording status indicator. Previously, when a file was opened for recording, some users had the assumption that it meant recording had started, because there was no obvious indicator. Now, indicators have been added to make it clear to the user if recording is currently on or not. We added text that states the number of hours and minutes recorded so far, a long status bar that changes color and reads either "Recording Paused" or "Recording Active”, and on the camera module, the file opened and time recorded is displayed at the top of the window


MINOR CHANGES

—Freeze display.  The user can now freeze the continuous stream display by either pushing the Freeze Display button, using the menu, or pressing the space bar.

—Hardware serial number and version display. This information is also appended to recordings.

—StateScript GUI improvements.  Macro buttons can be configured to enter StateScript commands to toggle DIO pins, etc. Also, there is improved labeling of the different panels. 

—Version update lookup.  Trodes will automatically lookup if a newer version is available.  In the intro menu, the ‘Information’ box will alert the user if a new version is available for download.

—Camera module export additions.  The camera module will now export linear track geometries, zone geometries, and scale info (cm per pixel).  It will also export linearized position if a linear geometry was enabled during tracking.

—If settings are changed to any nTrodes after a recording file is opened, the program will prompt the user to save an updated workspace alongside the recording (since the header at the top of the recording only captures the initial state).

-Multiple PETH windows open.  The user can open separate PETH windows for multiple nTrodes, which will automatically update when new events occur.

—Spike waveform alignment.  Spikes are now automatically aligned to the peak. This applies both the Trodes and the exportspikes program.

—Audio Off command line argument to disable audio.  On some machine, auto driver incompatibilities can cause crashes, so this was implemented to diagnose audio issues.

—For two-diode tracking in camera module, users can set if the red diode or the green diode is in the front.

 
CRITICAL BUG FIXES

None


MINOR BUG FIXES

—Python data extraction program fixed

—mergeSDRecording had incorrect error outputs that were removed

—Fixed the number of displayed columns to match what is in the workspace.

—Digital reference nTrodes are referenced by their in the workspace ID number rather than their index 

```


##1.5.2 
```
#!text
MINOR CHANGES

-Camera module now allows user to define which color LED is the ‘front’ using red-green position tracking.

BUG FIXES

None

```

##1.5.1 (February 28, 2017)
```
#!text
MINOR CHANGES

-Camera module now does not allow switching cameras once a recording file has been created. File must be closed before the camera can be switched again.


BUG FIXES

-Fixed minor memory leak from PETH windows

-Fixed Camera module time rate being recorded as 0 instead of 30000 

-Fixed bug causing StateScript GUI to crash when in standalone mode and opening the camera module


```


##1.5.0 (February 15, 2017)
```
#!text

MAJOR FEATURES

-Added Multi-Plot PETH functionality in spikes window. Users now have the option of seeing a histogram of all spike data points as well as histograms of individual, user-defined cluster plots at the same time. Clicking on specific cluster plots will open a PSTH window containing more specific data bout that cluster. Further, switching to a different nTrode will update the histogram window automatically.

-Automated testing system, specifically with regard to command line testing. These tests currently exist: a workspaces test (opens all workspaces and closes them among a set), a file playback test (opens all files in directory and checks to make sure all threads are syncing properly), a hardware connection test (tries communicating with a hardware setup), and a hardware streaming test to make sure data can be streamed from the hardware.

-Benchmarking GUI. Basically a settings control panel available in the main Trodes program, the Benchmarking GUI allows the user to edit benchmarking settings at runtime. Along with turning specific benchmarking tests on/off, the user can edit the frequency with which benchmarking statements are being printed (this is to prevent console clogging). These benchmarking settings can now also be saved and loaded via workspace files.

-Version information (compile date/time, git commit compiled, Trodes version info) can now be displayed from all modules in their ‘About’ tabs. This information is also saved in recordings and exported using the export utilities.

—Linux build system now automatically packages all required files into final build folder. All modules are compiled alongside Trodes main in all OS’s using Trodes_all.pro.

-Event system.  Events can now be defined and recieved by other modules for user-defined behavior.  Current events include spikes from defined units and zone enter/exit events from the camera module.



MINOR CHANGES

-Warnings are now thrown when opening Trodes if a separate instance of Trodes is detected to be running with an open workspace and Trodes will prevent itself from opening a workspace if this is the case to prevent networking issues.

-Trodes debug .txt logs are now automatically created in a folder directly adjacent to the Trodes executable, for windows/linux this is the same location as the binary/exe, and for mac the .app folder.



BUG FIXES

-Debug messages from modules now forwarded correctly to Trodes main on Windows

-Camera module auto gain behavior removed for GigE cameras

-Switching between two GigE cameras is now more robust to failed connections

-Switching from either of the signal generator sources no longer causes the next source to crash the system

-Rhythm source turned off by default to prevent crash when selected

-Improved button behavior and appearance across OS’s
```

##1.4.2
```
#!text
MAJOR FEATURES

** StateScript GUI now has a separate input widget to type commands to hardware. Users can use the up and down arrows to scroll through command history.

** Event System Added
  - Implemented an event system in Trodes server-client architecture that allows modules to dynamically create and send event messages to all other modules.
  - Added EventHandler class implementing a GUI menu allowing users to create connections between events and module actions. (e.g. when an event is received, all actions/methods linked to the event will be executed)
    - This class is highly portable and can easily be added to any module. (see documentation on the Trodes Wiki)

** CameraModule 
  - Position data streaming greatly expanded, the following data is now being streamed:
    - Linear position data (continuous)
    - Linear track data whenever it is created/edited/moved in a relative scale (0-1) format. (Non-Continuous)
    - Velocity data received from embedded Python. (Continuous)
    - See documentation on the Trodes wiki for specific format information
  - A compatibility mode was added that will either enable, or disable the changes to  position data streaming (modules that rely on the previous position data format will not be broken by this update).  

** Added new module: SimpleCommunicator
  - SimpleCommunicator is a heavily modified version of SimpleMonitor
which should be used as an example for how to set up a module to
request and receive data from other modules. (It should go hand-in-hand with the wiki for documentation purposes)
  - SimpleCommunicator currently supports full communication with Trodes and
CameraModule. (Notably, SimpleCommunicator will display CameraModule’s
real time position data)
  - SimpleCommunicator contains a fully implemented EventHandler.


MINOR FEATURES

** On Linux:
  - All executables can now be called directly. (I.e you can run Trodes, instead of Trodes.sh) The shell scripts have been removed.

** OfflineSorter exporter added to export programs

** Trodes
  - Added event system support to Trodes main module (it can now send events to other modules)
  - Added events to Trodes which track when spikes occur in user defied clusters in different NTrodes. These events are created when a new polygon is created in the ‘spike’ dialog menu.

** CameraModule
  - Several event definitions added at module startup (e.g. Zone created, linearization tract created) that can be seen by other modules.
  - Events added that tell the user when specific zones have been entered and exited by tracked objects.
  - Zones will now turn blue when entered by tracked objects and revert to clear when left.
  - Added UI element in Camera Module that brings up an eventHandler menu.
  - Added a plotLocation method action visible in the event connection menu that plots a point on the currently tracked position 


BUG FIXES

** CameraModule
  - Fixed an issue that would cause a crash if the user tried to create a linear track while tracking was enabled.
  - Fixed an issue that would cause a crash when double clicking the first node while drawing with either the Linear Track tool or the Range Line tool.
  - Fixed a bug in the load geometry code which would append a bad node onto the end of loaded linear geometry.

```

##1.4.1
```
#!text

MINOR FEATURES


**cameraModule:
	- Added debugging tool which prints all function calls as specific IDs in console output (set DEBUG_MODE define to 1 in videoDisplay.h)

BUG FIXES

**Export code can now keep going if an erroneous backwards timestamp is detected with -abortbaddata 0 flag

**cameraModule: 
	- Fixed an issue where cameraModule would try to track linear position on a rangeLine and cause a crash.

```

##1.4.0
```
#!text
MAJOR CHANGES

** Improved buffering of data to ensure that data are not lost even if the hardrive become inaccessible for over a second.  If the buffer is overrun, the system will now recover smoothly after the lost section.

**Embedded python in Camera Module.  Still a work in progress, but this will be used to allow custom processing of position information

**Optimized processing of video frames in Camera Module to reduce CPU load

**Added export programs, including ones for Phy Suite and Offline Sorter




MINOR CHANGES

** Improved graphics plotting in channel streaming window.  All done with Open GL.

** Camera Module linearization tool to project animal's position onto a track geometry. Bugs were fixed to allow saving and loading of geometry files.


```