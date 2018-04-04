[Wiki Home](Home) > [User Manual](Documentation) > [Command Line Calls](Command_Line_Calls)

**Table of Contents**

[TOC]

#Command Line Options

Trodes and its' modules have a large amount of command line arguments you can use to activate/deactivate certain features.  

##Primary Flags

The main command line flags are listed below, keep in mind that some of these flags have a set of subflags that go with them (see the hyperlinks in their respective descriptions):

| Flag | Description |
| --- | --- | 
| audioOff | Turns off all Trodes' audio.  More specifically, this flag prevents the audio thread from being initialized.  This flag is useful in rare cases when the audio thread will sometimes cause the main program to crash. |
| benchmarking | Starts Trodes in Benchmarking Mode.  This mode records system timestamps to enable latency testing, and is disabled by default for performance reasons.  There are a litany of benchmarking subflags documented in detail on the [benchmarking page](Benchmarking#markdown-header-command-line-call) of the wiki. |
| test | Enables automated testing functions in Trodes.  This is a debugging flag that will prevent you from actually using Trodes for the duration of the test selected.  Please see the [automated testing](#markdown-header-trodes-test) page for the different subflags/tests available. |

##Automatically loading a config or recording
When you call Trodes from the command line, you can also add a file after it to have Trodes automatically open it. For example: 

```
./Trodes /home/kevin/myworkspaces/32_Singles_ECU.trodesconf
```

will open Trodes and immediately open the configuration file specified. The same goes for any .rec file for playback. 

##[Benchmarking](Benchmarking#markdown-header-command-line-call)
Sometimes it is useful for users to test the latency of Trodes on their system. To do this, you can run Trodes in a 'Benchmarking Mode', and select a specific (or several) test to run. Latency is tested using the computer's current system time in milliseconds, measured from the start of the day. Follow the [link above](Benchmarking#markdown-header-command-line-call) for a detailed tutorial. 

##Testing Trodes
Trodes has a built in testing suite. More functionality will be added, but it currently supports testing workspaces, hardware connections, and playback files. 

To run, call Trodes with 'test'

```
./Trodes test
```

Located in the Testing directory, you can find three directories: WorkspaceLoading, WorkspaceHardwareStreaming, and Playback. 

- Trodes will look in the WorkspaceLoading directory for Trodes config files and attempt to load each of them. 

- Then it will look in the WorkspaceHardwareStreaming directory and attempt to make USB/ethernet connections. 

- Finally it will look in Playback and play 2 seconds of the recording. 

The final output will generate a report that looks like the following: 

```
#!text
********* Start testing of UnitTest *********
Config: Using QtTest library 5.5.1, Qt 5.5.1 (x86_64-little_endian-lp64 shared (dynamic) release build; by GCC 4.9.1 20140922 (Red Hat 4.9.1-10))
PASS   : UnitTest::initTestCase()
PASS   : UnitTest::workspace_loading()
PASS   : UnitTest::playback()
FAIL!  : UnitTest::hardware_connect_ethernet() 'connectionFailed == false' returned FALSE. ()
   Loc: [src-main/customApplication.cpp(246)]
FAIL!  : UnitTest::hardware_connect_usb() 'connectionFailed == false' returned FALSE. ()
   Loc: [src-main/customApplication.cpp(270)]
FAIL!  : UnitTest::hardware_stream_usb() 'numLoads==confFiles.length()' returned FALSE. ()
   Loc: [src-main/customApplication.cpp(357)]
FAIL!  : UnitTest::hardware_stream_ethernet() 'numLoads==confFiles.length()' returned FALSE. ()
   Loc: [src-main/customApplication.cpp(442)]
PASS   : UnitTest::cleanupTestCase()
Totals: 4 passed, 4 failed, 0 skipped, 0 blacklisted
********* Finished testing of UnitTest *********

```

As you can see, the computer used to run this test did not have any hardware connections set up, so all of the hardware_connect_* and hardware_stream_* tests did not pass.