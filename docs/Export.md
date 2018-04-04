[Wiki Home](Home) > [User Manual](Documentation) > [Exporting Data](Export)

**Table of Contents**

[TOC]

#Exporting Data

##[Export Functions](ExportFunctions)
The export utilities that run from command line extract specific data from the .rec files and outputs them to separate .dat binary files. These functions are detailed [here](ExportFunctions). 

The .dat files can then be imported into Matlab or Python to be used for further analysis.

##[Exporting Camera Module's Data](CameraModule#markdown-header-exporting-data)
The Camera Module is able to stream and export multiple different types of data including animal position data, an animal's linear position within a defined track geometry, the track geometry itself, and user defined zone data.  Refer to Camera Module's [exporting section here](CameraModule#markdown-header-exporting-data) for more details. 

##[Importing data into Matlab](Matlab)
In the TrodesToMatlab folder, there are Matlab functions that will read the binary data exported and return a Matlab data structure for further analysis in Matlab. There are detailed instructions in the **Instructions.txt** file in the directory, as well as [here](Matlab)


##[Importing data into Python](Python)
In the TrodesToPython folder, there is a Python function that will read the binary data in a .dat file and return a Numpy array with the data for further analysis in Python. More details can be found [here](Python).


##[SD Card Utilities](SDFunctions)

---