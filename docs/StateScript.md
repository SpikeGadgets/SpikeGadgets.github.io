[Wiki Home](Home) > [User Manual](Documentation) > [Modules](Modules) > [StateScript](StateScript)

**Table of Contents**

[TOC]


*Taken directly from [http://spikegadgets.com/software/statescript.html](http://spikegadgets.com/software/statescript.html)

#StateScript  
**A simple, yet powerful scripting language to control the timing of environmental input and output events with high temporal resolution in hardware.**

StateScript allows you to define temporally precise sequences of events and their triggers, which is useful with devices such as lights, levers, beam breaks, lasers, stimulation sources, audio, and solenoid pumps. Scripts are sent to our Environmental Control Unit and compiled to run in real-time in hardware. Controlling the experimental environment has never been so easy.

##A simple example

This complete example shows how to define variables, callbacks, and 'if .. else' blocks.

```
#!text
%all variables are global
int count = 0
int countThreshold = 10

%this block is executed if digital input 1 goes from low to high
callback portin[1] up

  if (count < countThreshold)  do

    %digital output 1 is turned to a high state
    portout[1] = 1 
    count = count + 1
		
    %this block is scheduled to be exectuted in 500ms.  
    %The 'in' keyword after 'do' is used to schedule for later.
    do in 500 
      portout[myPort] = 0
    end

    %a message is sent back to the computer. 
    %This is executed before the 500ms delay above is finished
    disp('Lever press') 

  else do
      disp('Ten presses completed') %a message is sent back to the computer
      count = 0 %reset the counter
  end

end;%semicolons are used to compile/execute everything since the last semicolon.  
```

##Language overview
###1. Callbacks

Callbacks are executed when the hardware input state changes. The user can create callbacks for individual digital input ports. A callback may not be nested inside another block.

```
#!text
%this callback is executed if digital input 2 goes from low to high
callback portin[2] up

  portout[1] = 1
  
end

%this callback is executed if digital input 2 goes from high to low
callback portin[2] down

  portout[1] = 0

end;
```

###2. Functions

Functions are blocks that are executed with the 'trigger()' command. A trigger may not be nested inside another block.

```
#!text
%function 1 (exectuted with 'trigger(1)')
function 1
	
  portout[1] = flip %'flip' will switch the binary output state

end

%function 2 (exectuted with 'trigger(2)')
function 2
	
  portout[2] = flip 

end

%this callback is executed if digital input 2 goes from low to high
callback portin[2] up

  trigger(1) %this will execute function 1
  
end;
```

###3. Variables

Variables are global, which means they are accessible from all blocks. They must be defined outside all blocks. Currently only integer variables are supported.

```
#!text
%Variables are declared outside all blocks. 
int currentPort = 1;

function 1

  portout[currentPort] = flip %variables can be used in portout[]
  currentPort = currentPort+1 

end;
```

###4. 'Do in' blocks

The workhorse of StateScript timing control, 'do in' blocks allow you to schedule an entire block of code to be executed later with 1 ms precision.

```
#!text
int blinkDelay = 500

callback portin[1] up 

  portout[1] = 1 %turn on light now
  do in blinkDelay
    portout[1] = 0 %turn off light in 500 ms
  end
	
end;
```

###5. 'If...else' blocks

The if...else block in StateScript allows conditional block execution. Standard boolean logic notation (&&, ||, <, >, <=, >=, ==) are used to define conditions. 'If' statements can be combined with 'do in' statements.

```
#!text
int currentCorrectChoice = 1
int numberOfCorrect = 0

callback portin[1] up 

  %Combo if, do in statement 
  if (currentCorrectChoice == 1 && numberOfCorrect  > 3 ) do in 500

    numberOfCorrect = numberOfCorrect+1
    portout[1] = 1 
  
    %turn off reward pump 1500ms after this block is executed
    %(2000 ms after callback execution) 
    do in 1500
      portout[1] = 0 
    end

  else do
 
    numberOfCorrect  = 0
    
  end
	
end;
```

###6. 'While...then' blocks

The while...then block is used to repeatedly schedule a block of code at regular intervals until a condition is no longer true. Every 'while' statement must be accompanied by a 'do every' statement.

```
#!text
int count
int loopInterval
int done

function 1
  
  %only start blinking if it is not already blinking
  if (done == 1) do
    
    count = 0
    done = 0
    loopInterval = 500

    %blink the light 16 times
    %as long as the condition is true, the block is re-scheduled 
    %every loopInterval milliseconds
    while count < 16 do every loopInterval 
      portout[1] = flip 
      count = count+1
      
      %we can make the loop interval change as the while loop is going
      loopInterval = loopInterval-10  

    %when the condition is no longer true, the 'then' statement
    %is exectuted to reset conditions
    then do 
      portout[1] = 1
      done = 1
      loopInterval = 500
    end

end;
```

###7. Output control

Currently only digital port output is supported. Analog output control coming soon.

```
#!text
portout[1] = 1  %turn output port 1 to a high state
portout[currentBlinkingPort] = 0 %turn a variable-defined port to a low state
portout[currentBlinkingPort] = flip %flip a port state
```

###8. Built-in functions

Built-in functions are included to allow extra features and custom hardware behaviors.

```
#!text
%'clock()' is used to get the current clock value
a = clock()
a = clock(reset) %used to reset the system clock to 0

%'disp()' is used to send text back to the computer interface
disp(a) %display a variable 
disp('Rewarded!') %display a string

%'random()' is used to generate random numbers
a = random(99) %returns a random number between 0 and 99

%'sound()' is used to play an audio file stored on an SD card
sound('beep1')
sound(stop) %stops audio playback before it is done
volume(10) %change the current audio volume

%'trigger()' is used to trigger a defined function
trigger(2) %execute function 2
 
%By default, hardware port status updates 
%are sent to the computer every time a digital port changes 
updates off %turn all DIO auto status updates off
updates off 1  %turn all DIO auto status updates off for input port 1
updates on %turn all DIO auto status updates on
```

###9. The semicolon ( ; )

In most of the examples above, a semicolon, followed by a carriage return, is placed after the last line of the script. When a script is sent to the ECU, the ECU stores multiple lines until a semicolon is given. This tells the ECU to compile everything up to that point (since the last semicolon). Semicolons should not be placed inside any blocks. A single command followed by a semicolon is executed immediately (portout[1] = 1;). The hardware will return '~~~' if everything compiled ok. Otherwise it will return an error message.

---

##Considerations

###Asynchronous execution

StateScript execution is asynchronous, which can be confusing to programmers who are accustomed to synchronous execution. When a program executes asynchronously, it does not wait for one line of code to finish before moving on to the next line. Using a 'do' block without an 'in' statement forces immediate, synchronous execution of the block, but a 'do in X' statement simply adds the block of code for later execution. The compiler moves on to executing lines that follow the scheduled block before the scheduled block is executed. 

```
#!text
%What is the final state of the output port?
function 1
  portout[1] = 1 %exectuted 1st 
  do in 500
    portout[1] = flip %exectuted 3rd
  end
  portout[1] = 0 %exectuted 2nd
end;	
```

Considering this asynchronous behavior is especially important for while...then loops. These loops are not the same as traditional while loops, where code following the loop block is not executed until the loop is finished. Instead, only the first iteration of the loop is executed immediately (and synchronously), at which point the second iteration is scheduled. When the next scheduled iteration exectutes, the condition is first checked, and if the condition is true the block is executed and the next iteration is scheduled. Therefore, any variables that are counting the number of iterations, etc., need to be reset in the while loops' 'then' section, which is executed once the condition is no longer true. 

Shown is an example with two while loops, one nested inside the other. The counters for these loops are managed in the 'then' statements:

```
#!text
int pulseCounter = 0
int trainCounter = 0

callback portin[1] up
  
  %create 10 pulse trains, where the trains 
  %start every 100 ms and each individual
  %train contains 5 pulses at 10 ms intervals
  
  %outer while loop 10 pulse trains
  while trainCounter < 10 do every 100
    
    %nested inner while loop for each train 
    while pulseCounter < 5 do every 10

      %create a single 1 ms pulse
      portout[1] = 1
      do in 1
        portout[1] = 0
      end
      pulseCounter = pulseCounter + 1

    then do %cleanup for inner loop

      %reset inner loop counter
      pulseCounter = 0 
      
      %iterate outer loop counter here
      trainCounter = trainCounter + 1 
    end

  then do %cleanup for outer loop
      
      %reset outer loop counter
      trainCounter = 0 
  end
end; 
```