[Wiki Home](Home) > [User Manual](Documentation) > [Modules](Modules) > [CameraModule](CameraModule)

**Table of Contents**

[TOC]

#Camera Module
Camera module has two modes: Record and playback. From Trodes, camera module can be opened by clicking on **Video**, or can automatically open if specified in the workspace configuration. Camera module will open in the same mode Trodes is in, either record or playback. Camera module will open in playback mode if opened alone without Trodes. 

##Recording Video
Open Trodes, open a workspace configuration, and then open camera module. Click on the recording button. The indicator in Trodes should turn green and start counting the time recorded. Camera module should also specify a file that it is recording to and the amount of video recorded. 

##Position Tracking
The camera module has the ability to track the animal's movement, either during or after the experiment.  Simply click the "Track" button to enable position tracking.  
You can modify the tracking function's settings by pressing the "Settings" button.

##Exporting Data
Camera Module has the ability to export tracked two dimensional (absolute coordinate based) and linear (relative position 0.0-1.0 on a defined linear track) as well as its geometry. 
 
__NOTE:__ All extracted binary files can be read into Matlab using a single function: readTrodesExtractedDataFile.m (in the TrodesToMatlab toolbox). This function will read the file's header info and data into a Matlab structure. Similarly, the files can be read into Python using readTrodesExtractedDataFile.py (in the TrodesToPython folder). 

###Two Dimensional and Linear Position Data
Both two dimensional and linear position data can be exported to separate files with the .videoPositionTracking and .videoLinearTracking extensions respectively.  This can be done simultaneously during recording or when playing back a file in Camera Module's standalone mode.    

To export tracked position data during recording by simply pressing the "Track" button after opening a new .rec file.  This will export both two dimensional and linear tracking data to their respective files.  __NOTE:__ To export linear position data you must have first defined a Linear Track using Camera Module's 'Linear' Tool.

###Off-line tracking
To export (or re-export) tracked position data from a previously recorded video file, launch cameraModule in standalone mode (i.e. run the cameraModule executable directly, rather than launching it from Trodes. Then click 'File' to open the desired *.h264 video file. Set up your track geometry (or load it in using the File menu), adjust your tracking settings (under the 'Settings' button), and set the start/end time points for tracking using the small circular sliders in the time bar. You can play the video and skip around to make sure you are happy with the tracking/linearization. 

When you are ready to write out your tracking data, press the "log" button.  Select a file name and save location, use the scrubber to reset the video to the desired start point, and then press the green play button.  This will export all position data from your current position in the video file onwards.  Like before, you must have a Linear Track defined to export a linear tracking file.  

__NOTE:__ The "Log" function may occasionally pause while scanning the file.  This happens when Camera Module cannot find one of the LEDs it is using for tracking the animal's location. You can scrub forwards and backwards one frame at a time to inspect the local tracking information. If everything looks correct, click the play button to continue the tracking. If not, you can manually update the LED position on a frame-by-frame basis ('Fix mode')

In 'Fix mode', you can manually update the estimated position of the tracking LEDs. (In 2-LED mode, left-click to update the position of the front/green LEDs, and right-click to update the position of the rear/red LED array.) Note that if you update the LED position, then scrub backwards and forwards in time using the arrow keys, you will (apparently) lose your updates, so inspect the frames before making your fixes.

When you have finished with the manual fix, click the play button to continue automatic tracking.

Once you have completed tracking for the session, click the 'Log' button again to close the tracking files. 

###Geometric Data
Using Camera Module's tools it is possible for the user to superimpose a variety of different geometries over the video feed.  You can export several of these geometries, specifically Linear, Range Line, and Zone geometries, in the following fashion:

1. Navigate to the 'File->Geometry->Save geometry...' menu.

2. Select which type of geometry you want to save, pick a name and location to save the geometry file at in the popup file browsing dialog, and click 'Save'.

3. The selected geometry is now saved to a file with the '.trackGeometry' extension.

All geometries are also automatically saved when utilizing the 'Log' function in playback mode, and when making a new file recording.

##Event Detection
The camera module also has the ability to detect events and plot the animal's position when they occur. For example, by clicking on the **Tools** button and selecting **Zone**, you can draw a polygon with the mouse. Set up an event connection, detailed below, and when the animal enters the zone, a point will be plotted on the video. You can also plot points at the animal's position when a spike enters a defined cluster. 

##Using the Event Menu

When you open the EventHandler dialog menu for the first time you will be greeted by the ‘Event Connections” window, it should be empty.  

###Adding a New Connection

1. Click the ‘Add’ button or press the enter key.

    * This brings up the ‘Event/Action Menu’ window.  In the left box are all the events currently visible to your module (this list will be updated automatically when new events are created).  The right box contains your module’s exposed methods.

2. Select one of the events from the list in the ‘Visible Events’ box and then select the action you want to connect that event to from the ‘Available Actions’ box.  Click ‘Ok’ or press the enter key to create the connection.

3. The ‘Event/Action Menu’ window should close and the newly created connection should now be visible in the ‘Event Connections’ window.

###Removing a Connection

To remove a currently active connection, simply select the the connection you want to remove in the ‘Event Connections’ window, and press either the ‘Remove’ button or the delete key on your keyboard.

###Known Issue

* If you press either the ‘Remove’ button or the delete key without first selecting a connection in the ‘Event Connections’ window, the topmost connection will be deleted.



##Arguments
Camera module can be opened with arguments, either specified in the workspace configuration or if opening alone, directly in command line. If opening camera module in Trodes, these arguments will be created and passed automatically by Trodes. 
```
-resolutionx          Resolution of width 
-resolutiony          Resolution of height
-trodesConfig         Specify a Trodes config file
-threshold            Specify a threshold value
-cameraNum            Used by Trodes to number multiple cameras
-playback             Used by Trodes to tell the module to open in playback mode
-serverAddress        Used by Trodes to let the module connect 
-serverPort           Used by Trodes to let the module connect 
```