---
layout: page
title: Network API Guide
---

- [Introduction and motivations](#introduction-and-motivations)
- [Setup](#setup)
    - [Python](#python)
    - [C++](#c)
- [Basics](#basics)
    - [AbstractModuleClient](#abstractmoduleclient)
    - [High Frequency Data Streaming](#high-frequency-data-streaming)
        - [Sending data](#sending-data)
- [Advanced](#advanced)
    - [Hardware interface](#hardware-interface)
- [Example](#example)

## Introduction and motivations

As the Trodes software suite grows, the need for extensibility grows as well. It is not feasible for us to continue to program and support modules that run during experiments for each of our users, so a new software library was written to accomodate the need. The library provides an Application Programming Interface (API) for connecting to Trodes and subscribing to messages and data. Users can import the library (or compile against it in C++) and write their own online analysis code using the data streamed from Trodes, and even stream out their own data for another module to use. 

In addition, by making it easier to write modules, we are also making it easier to split up the processing work between different processes. That makes it easier to distribute the live processing pipeline onto different machines.

All code is still currently in development and is only tested on Ubuntu 16.04 machines. Over time, we will port it over to be available on Windows as well.

## Setup

### Python

We currently only support Python 3. Python 2 is in the process of being deprecated and as a result, we will not provide a package for it. The current method to install the library in Python is using `pip` on a `tar.gz` file we will provide. Download the `tar.gz` file and run the following: 

```bash
#Run as admin using sudo if needed
python3 -m pip install trodesnetwork.tar.gz 
```

That command will install the package into your Python installed modules path. It will also download `numpy` as a dependency, but most scientific code in Python should be using that anyways.

### C++

C++ setup is a little different as it is a compiled language. We have not packaged the library for C++ yet, but the goal will be to distribute it in a directory containing `include`, `src`, and `lib` sub-directories, where you can program using the interface in the `include` files, and link to the files in the `lib` directory.

More coming soon.

## Basics

### AbstractModuleClient

The main class that all networking activity starts from. This class represents a client and connects to the server (run on Trodes). To use it, write your own class that inherits from `AbstractModuleClient`.

```python
import trodesnetwork

class MyCustomClient(trodesnetwork.AbstractModuleClient):
    ... 
```

There are also a number of functions you can overload. These functions are automatically called whenever specific messages are received by the client, such as commands to quit, subscribed events, file opened by Trodes, etc. This class is primarily used for passing messages involving the state of the programs and configuration variables.

```python
class MyCustomClient(trodesnetwork.AbstractModuleClient):
    def recv_quit(self):
        print('Python received a quit command')
        run_quit_procedure()
    
    def recv_file_open(self, filename):
        print('Trodes opened a file called', filename)
        custom_fileopen_function(filename)

#Connect and initialize
network = MyCustomClient("ClientName", "tcp://127.0.0.1", 49152) #name, address, port
if network.initialize() != 0:
    print("Network could not successfully initialize")
    del network
    quit()

```

One thing to note is the fact that AbstractModuleClient, along with all of the library's code, is written in C++ and executes compiled C++ code in Python. AbstractModuleClient will launch background threads to receive and send from the network sockets. There may be complications with that model that will need to be sniffed out during alpha testing.

### High Frequency Data Streaming

When it comes to high speed data, there is another set of classes that is built to handle that. `AbstractModuleClient`, while having messaging capabilities, should not be used to send high speed data (>500hz). We have two classes to handle that: `HighFreqPub` for publishing data and `HFSubConsumer` for subscribing to data. `AbstractModuleClient` will still manage the setup and broadcast the configuration details of these objects for you, but once created, they are separate objects with separate threads and sockets.

#### Sending data

Send data with `HighFreqPub`. This feature is not supported in Python yet. Here is an example in C++: 

```cpp
unsigned char *buffer = new unsigned char[24];
HighFreqPub *pub = new HighFreqPub();
pub->initialize();
while(!quit){
    pub->publishData(buffer, 24);
}
```

What the code is doing:

1. Allocate 24 bytes for a buffer
2. Create and initialize the `HighFreqPub`
3. In a loop, send out the data using the publisher.

What we do not show here, and which is currently out of scope of this guide, is the serialization of your data into a buffer of bytes. For one possible method, please take a look at our [basic serialization library](https://github.com/spikeGadgets/Seriallib).

## Advanced

### Hardware interface

Trodes has the ability to interact with the hardware, and with our new hardware, also send stimulation commands. The network API will also have the ability for modules to send commands for Trodes to forward to hardware on their behalf. See the following for an example: 

```python
network.initializeHardwareConnection()

s = trodesnetwork.StimulationCommand()
s.setGroup(2)
s.setSlot(1)
s.setChannels(1, 1)
s.setBiphasicPulseShape(5, 5, 5, 5, 5, 20, 5)

if s.isValid()
    network.sendStimulationParams(s)
    network.sendClearStimulationParams(1)
    network.sendStimulationStartSlot(1)
    network.sendStimulationStartGroup(2)
    network.sendStimulationStopSlot(1)
    network.sendStimulationStopGroup(2)
```

1. As you can see, the first thing to do is to initialize the hardware connection. This is because for low latency, hardware messages are sent via a separate socket sent directly to Trodes and that socket will need to be initialized. 
2. Next, a StimulationCommand object was created and parameters set, randomly for the example.
3. Finally, the user can send the parameters over to the hardware using `sendStimulationParams`. 
4. To trigger a start or stop command for any of the groups/slots, call the `sendStimulation(Start/Stop)(Group/Slot)` functions.

## Example
