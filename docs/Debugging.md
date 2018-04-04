[Wiki Home](Home) > [Developer Documentation](DevDocumentation) > [Debugging](Debugging)

**Table of Contents**

[TOC]

#Debugging Trodes
This is a growing guide for anyone that is contributing to Trodes development and encounters a bug. 

##Compiler errors and warnings
Compilers can only catch so much, and is not able to read developers' intentions. That being said, the first thing to do is to fix any compiler errors and warnings. Warnings pop up for code that won't necessarily cause a crash alone, but can cause a crash indirectly in a different location (uninitialized variables, bad casting, etc). Errors pop up for code that can't compile. After fixing errors, fix as many warnings your code introduced as you can. 

##Print statements
The most basic of all debugging methods. Add a qDebug() in one, two, or a million spots, and you can find the line where the program crashes and print out values of variables. Now this is very tedious, and is **should be** a last resort. Sometimes, a quick qDebug() statement will find you the problem, and sometimes it'll waste you an afternoon. 

##Sanity Check
Coding for an extended time period is stressful and draining. Don't go insane by doing the following when debugging: 

1. Doing a fresh qmake, clean, and rebuild
2. Removing any extra files from the bin directory that crept in
3. Check if the bug exists in a previous commit or if you accidentally snuck it in
4. Come back to the bug after lunch. 

##Crash reports
If your OS supports it, viewing a core dump or crash report is invaluable. 

###Linux
As long as Trodes is built in profile or debug mode, a core dump will be available in the directory of the executable. Use the gdb tool to open the debugger and read the function stack. This will let you know which function and sometimes the line number the program crashed on. 

```
#!sh
gdb ./Trodes core.23858
bt
```

To enable core dumps, follow these steps: 

Edit the file /etc/security/limits.conf (as root), to include the following lines (NB: The line begins with an asterisk):

```
# (Trodes) Enable core dumps
* soft core unlimited
```
		
Edit /etc/sysctl.conf (as root) and add the following lines:

```
# (Trodes) Include process id (PID) in core dump file names
kernel.core_uses_pid=1
```

Log out and log in again for changes to take place. 

###Windows
To my knowledge, Windows does not generate a core dump. The next best thing is to use the Windows Event Viewer, navigate to custom applications, and look for an application event that caused a crash. The report should provide the program name that crashed, an error code, and the offset of the line of the executable binary. 

###Mac


##Debuggers
For a more involved bug, debuggers often need to get used. Debuggers allow you to step through code line by line, monitor variable values, and pause the program. Unfortunately, debuggers do not play well with multithreaded code, and bad crashes may occur. 

- Make sure you are building with the Profile build. This keeps the executable just like the Release build but generates an extra file for debugging symbols. 
- If your bug is not on the main GUI thread, be careful, things can be deceiving in the debugger as threads run at different speeds than normal
- Do the sanity check above from time to time 

Otherwise, if the program doesn't break with a "FATAL ASSERT" (which does happen on specific combinations of operating systems, builds, and versions due to multithreading and global configs), continue debugging. You can: 

- Add breakpoints to functions, memory locations, and expressions
- Pause and check variables
- Change values during runtime

##Valgrind
Valgrind can sometimes be helpful for finding memory errors and leaks. Memory errors occur when a location of memory is improperly accessed, and leaks occur when memory is not deallocated. Read [this valgrind tutorial](https://web.stanford.edu/class/cs107/guide_valgrind.html) for an intro to Valgrind

##Helgrind
Helgrind is a valgrind tool for multithreading that looks for potential race conditions and deadlocks. Read the [official manual](http://valgrind.org/docs/manual/hg-manual.html) for more information. To use it with Qt, follow [this tutorial](http://www.kdab.com/~dfaure/helgrind.html).