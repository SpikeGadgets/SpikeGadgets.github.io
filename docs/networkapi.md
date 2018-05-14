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
        - [Receiving Data](#receiving-data)
            - [**Trodes Data**](#trodes-data)
            - [**Other streaming data**](#other-streaming-data)
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
python3 -m pip install spikegadgets_python
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
        recvquit = true
    
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

#### Receiving Data

Receive data with `HfSubConsumer`. In a nutshell, this class creates a background thread for the socket, immediately stores new messages into a circular buffer, and allows the user to pop off the data at their convenience.

##### **Trodes Data**

If you are subscribing to LFP data, or any of the data streams Trodes publishes, you can use one of the many helper functions such as `subscribeLFPData`, `subscribeSpikesData`, etc. Trodes currently publishes the data streams LFP, Spikes, Analog, Digital, and Neural.

```python
#Create internal buffer of 100 messages (data points), and subscribe to ntrodes 1, 2, 5, 6, 7, 8, 10
datastream = network.subscribeLFPData(100, ['1','2', '5', '6', '7', '8', '10'])
datastream.initialize()
```

The object created for suscribing to Trodes data is a derived class of `HfSubConsumer`. To fetch the data from the object's internal buffer, we utilize numpy. Here's the rest of the example followed by further explanation:

```python
buf = datastream.create_numpy_array()
timestamp = 0
while not recvquit:
    #Get the number of data points that came in
    n = datastream.available(1000) #timeout in ms
    for i in range(n):
        timestamp = datastream.getData() 
        processDataBuffer(buf)
```

1. Create a numpy array from the object, proper size and dtype done for you.
2. Enter into a while loop. The `recvquit` variable is changed in this example by the `recv_quit` overloaded function above.
3. Call the `available(timeout)` function, where you check how many messages are in the buffer. If none then it will wait for the specified number of milliseconds. 
4. `getData()` automagically pops the data off the internal buffer and places it into the numpy array `buf`. 
5. Process the data with whatever you want. Keep in mind that the numpy array is a read-only structure.

##### **Types of Trodes Data**

1. `subscribeLFPData(buffersize, ['1', '2', '2', '3'])` - Get LFP data in an array. Pass in:
    - the max number of data points to hold 
    - a list of nTrodes (as strings) to subscribe to
2. `subscribeSpikesData(buffersize, ['3,0', '4,1', '5,0'])` - Get Spike data. Pass in:
    - the max number of data points to hold
    - a list of strings, specifying the ntrode and cluster. A 0 cluster is unclustered data. These clusters can be drawn and created by the user in Trodes
3. `subscribeNeuralData(buffersize, ['1,1', '1,2', '2,1', '2,2'])` - Get raw, unfiltered data from channels. Pass in: 
    - the max number of data points to hold
    - a list of strings, specifying the ntrode and ith channel, as displayed in the Trodes stream window.
4. `subscribeDigitalData(buffersize, ['ECU,Din12', 'ECU,Din13'])` - Get digital data from digital inputs. Pass in:
    - the max number of data points to hold
    - a list of strings, specifying the device and digital channel in each string, as specified in the trodes configuration file.
5. `subscribeAnalogData(buffersize, ['ECU,Ain1', 'headstageSensor,GyroX'])` - Get analog data from analog inputs. Pass in:
    - the max number of data points to hold
    - a list of strings, specifying the device and analog channel in each string, as specified in the trodes configuration file.

##### **Other streaming data**

The Trodes data stream objects were created specifically and optimized for Trodes Data. You can subscribe to other modules' data streams via the `HFSubConsumer` class. The best way to create an `HfSubConsumer` class is to use the `subscribeHighFreqData` member function, part of the `AbstractModuleClient` base class. While you can manually instantiate the `HfSubConsumer` class, the network client will search available data streams and automatically create and subscribe the object for you. 

```python
datastream = network.subscribeHighFreqData('PositionData', 'CameraModule', 60)
datastream.initialize()
```

Because this is a generic subscriber with no knowledge of the data provided, you must instantiate the numpy array yourself. Here's how to do it:

```python
ndtype = datastream.getDataType().dataFormat  #String containing numpy dtype
nbytesize = datastream.getDataType().byteSize #Number of bytes in each message

dt = numpy.dtype(ndtype)                      #Create the actual dtype from the string
buf = memoryview(bytes(nbytesize))            #Create a memoryview handle
npbuff = numpy.frombuffer(buf, dtype=dt)      #Create the numpy array
```

And because you manually created the numpy array, when calling `readData`, you must pass in the `memoryview` object you created, followed by the number of bytes. 

```python
while stillrunning:
    n = datastream.available(1000)
    for i in range(n):
        byteswritten = datastream.readData(npbuff)
```

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
s.setGroup(1)               #Group
s.setSlot(1)                #Slot
s.setChannels(1, 1, 1, 1)   #(Cathode NTrode ID, cathode channel, anode ntrode id, anode channel)
s.setNumPulsesInTrain(5)    #Number of pulses in train

#(leading pulse width samples, 
#leading pulse amplitude[0-256), 
#second pulse width samples, 
#second pulse amplitude [0-256), 
#interphase dwell samples, 
#pulse period samples, 
#start delay samples)
s.setBiphasicPulseShape(100, 128, 100, 128, 5, 2000, 5)

if s.isValid()
    network.sendStimulationParams(s)
    network.sendStimulationStartSlot(1)
    network.sendStimulationStopSlot(1)
```

1. The first thing to do is to initialize the hardware connection. This is because for low latency, hardware messages are sent via a separate socket directly connected to Trodes and that socket will need to be initialized. 
2. Next, a StimulationCommand object was created and parameters set. In this case, the parameters are saved in group 1, slot 1.
3. Finally, the user can send the parameters over to the hardware using `sendStimulationParams`. 
4. To trigger a start or stop command for any of the groups/slots, call the `sendStimulation(Start/Stop)(Group/Slot)` functions.

**Note:** If a slot's stimulation train is running, then any stimulation params sent for that particular slot will be ignored. There currently is no interface available to check if the parameters were set or ignored, so if you want to absolutely guarantee the setting of parameters for a particular slot, you'd have to first call `network.sendStimulationStopSlot(1)` followed by the call to `network.sendStimulationParams(s)`. The stim train will stop and then be open to receiving new parameters.

## Example

Run this example after opening Trodes and starting streaming (from any source). Assumes Trodes network is located on 127.0.0.1 and port 49152, the default location address.

```python
from spikegadgets import trodesnetwork as tnp
import numpy
#Define this for breaking loop, comes in later
stillrunning = True
def stoploop():
    global stillrunning
    stillrunning = False


#Definition of network client class
class PythonClient(tnp.AbstractModuleClient):
    def recv_quit(self):
        stoploop()


#Connect and initialize
network = PythonClient("TestPython", "tcp://127.0.0.1",49152)
if network.initialize() != 0:
    print("Network could not successfully initialize")
    del network
    quit()

datastream = network.subscribeLFPData(100, ['1','2', '5', '6', '7', '8', '10'])
datastream.initialize() #Initialize the streaming object

buf = datastream.create_numpy_array()
timestamp = 0
#Continuously get data until Trodes tells us to quit, which triggers the function that flips the "stillrunning" variable
while stillrunning:
    #Get the number of data points that came in
    n = datastream.available(1000) #timeout in ms
    #Go through and grab all the data packets, processing one at a time
    for i in range(n):
        timestamp = datastream.getData() 
        print(buf)
#Cleanup
del network
```