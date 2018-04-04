[Wiki Home](Home) > [User Manual](Documentation) > [Exporting Data](Export) > [Importing to Python](Python)

**Table of Contents**

[TOC]

#Importing data to Python
The Python script to import data is currently limited to a single script and requires the module numpy to be installed. 

It reads in a single binary data file and return a python dict, which contains a numpy array stored in the 'data' field. 

It will read in .dat files created by the export functions, and create a numpy array. For example, if the fields section states 

```
Fields: <time uint32><LineSegment uint16><RelativeLinearPos double>
```

Then the script will return a numpy array like so: 

```
>importedData['data']
[(160157682, 0,  0.23706176), (160159750, 0,  0.26997685),
(160161938, 0,  0.34283835), (160165086, 0,  0.42896333),
...
(160168038, 0,  0.492976  ), (160170927, 0,  0.55913753)]
```

Some camera module outputs like position data will create a binary file, which can also be parsed with this function.