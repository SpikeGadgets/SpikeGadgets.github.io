[Wiki Home](Home) > [User Manual](Documentation) > [FAQs](FAQ)

[TOC]

#Frequently Asked Questions
##How do I ...
##Inform developers of a bug?
Write down the exact scenario that happened, everything that happened the moment you started Trodes until the bug or crash, and what you were clicking when it happened. Open the debugLogs folder and in the file created under that session, at the top, note the version, compile time, and Git commit. 

Provide the developers with the debug log, your exact actions, operating system, and if possible, the files you were using. 

##Why did Trodes just ...

###Crash after ~0.5 seconds of streaming on Windows?
This may be due to an audio driver issue, found on our Dell XPS machines and some other windows machines. The solution is to open the folder containing Trodes in the command prompt, and run Trodes like the following: 

```
Trodes.exe audioOff
```

This will disable the audio driver to prevent the crash.