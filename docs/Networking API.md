[Wiki Home](Home) > [Developer Documentation](DevDocumentation) > [Trodes Network API](Networking API)

#Trodes Network API:

[TOC]
##Python 3.5
We currently support Python3.5 and above. Any other versions are not guaranteed to work. Nearly all functionality in C++ is also supported in Python for Trodes, but may be used slightly differently in Python. 
##TrodesMsg:
Message class to send over the network. Using templates and references, TrodesMsg packages and unpackages easily and intuitively into objects that are easily sent over the network

You can pack a variable number of values into the message, and use the format string to denote the type and order.

###Supported Types (format character, type)
* ('i', int)
* ('1', uint8_t)
* ('2', uint16_t)
* ('4', uint32_t)
* ('8', uint64_t)
* ('s', std::string)
* ('b', std::vector <byte>)
* ('n', NetworkDataType) - Explained in next section

If other data types are passed, the c++ compiler should throw errors. If the format string is not correct, the message will stop parsing and print out an error. This is because the compiler can catch bad types, but cannot catch dynamic format strings at compile time.
###Python
TrodesMsg was created to support messages containing dynamic types in C++. In Python, lists are already dynamic so in place of a TrodesMsg, pass in a list of message contents. 
```python
msg = ["foo", 1234, 5.06]
client.sendMsgToTrodes("subjectline", "sid", msg)
```
###Creating a message:
Creating a message involves passing variables by value 

* *TrodesMsg()*
	* Creates an empty message
* *TrodesMsg(std::string f, ... )*
	* Creates a message from a format string, followed by a variable list of values
```cpp
int a=0, b=1; 
std::string s = "hello"; 
TrodesMsg message("iis", a, b, s);
```
* void createcontents(std::string f, ...)
	* Appends the format f and the corresponding values to the message
```cpp
uint32_t time = 1234567;
message.createcontents("4", time); 
//msg now has [0,1,"hello",1234567]
```
###Reading from a message
Reading from a message involves passing uninitialized variables by reference, and having TrodesMsg 'fill' in and transfer values to the variables

* *void transfercontents(std::string f, ... )*
	* Transfers the message's contents to the passed variables
```cpp
int x, y;
std::string t;
uint32_t time2;
message.transfercontents("iis4", x, y, t, time2);
//Now x=0, y=1, t="hello", and time2=1234567
```

###Copying a message
Sending a message through the network will "consume" the message. After it is sent, a TrodesMsg will become empty. To copy a message, call the copy() function. Setting a TrodesMsg equal to another will NOT copy it, it will assign the underlying pointer equal to each other. 

* Copy Constructor: *TrodesMsg(const TrodesMsg &t)*  
	* equal (=), passing by value to functions, returning by value
	* Shallow copy occurs (underlying pointer to zmsg_t is copied, not its values)
* *TrodesMsg copy()*
	* Returns a deep copy of the message

###Useful functions
A number of useful functions for the class

* *void addstr(std::string str)* and *std::string popstr()*
	* Appends a string and pops a string from the message
	* Useful when all you need to send are strings 
	* Careful, there are no checks if the next content is actually a string or not
* *size_t numContents()*
	* Number of contents in the message
* *size_t size()*
	* Total byte size of the contents
* *void setformat(std::string value)*
	* Sets the format of the message (usually done automatically)
* *std::string getformat()*
	* Gets the message format
* *zmsg_t* getzmsg()*
	* Returns a pointer to the underlying zmsg_t

##AbstractModuleClient:
Base class to inherit for creating network clients for Trodes modules

###Constructor
* *AbstractModuleClient(const char \*id, const char\* addr, int port)*
    * id: name/network id for the module client
    * addr: broker address to connect to (default "tcp://127.0.0.1")
    * port: port to connect to (default 49152)
    * Default address/port are macros DEFAULT_ADDRESS and DEFAULT_PORT


###Functions to overload
These functions are called when the module client receives a the type of message

* *void recv_event      (std::string origin, std::string event, TrodesMsg &msg)* 
    * origin: origin of event
    * event: name of event
    * msg: TrodesMsg containing event information

* *void recv_file_open  (std::string filename)*
    * filename: name of file trodes just opened

* *void recv_file_close ()*
    * trodes just closed its file

* *void recv_source     (std::string source)*
    * source: name of source
    * Trodes just connected to a source ("None", "Generator", "File: ", "Ethernet", "USB", "Rhythm", "Spikes Generator")

* *void recv_acquisition(std::string command, uint32_t timestamp)*
    * command: possible string values are "PLAY","PAUSE","STOP","SEEK"
    * timestamp: timestamp the command was sent at
    * Trodes just did a play/pause/stop/seek. 
	    * PLAY can be done during recording and playback
        * STOP can be done in recording only, and indicates data acquisition and recording has stopped
        * PAUSE indicates that the file playback has just been paused
        * SEEK indicates that the file position was moved

* *void recv_quit       ()*
	* Trodes is telling the module to quit

* *void recv_time       (uint32_t time)*
    * time: requested timestamp from trodes after the module requested one. latency is ~0.75ms, +/-.25ms

* *void recv_timerate   (int timerate)*
	* timerate: requested timerate
    * the timerate that the module requested



###Public functions
* *std::vector &lt;Event&gt; getAllEvents()*
    * returns vector of all events, where Event struct is {event, origin}

* *void subscribeToEvent(std::string origin, std::string event)*
      void unsubscribeFromEvent(std::string origin, std::string event)
	* origin: network id of who produces this event
    * event: name of the event
    * start/stop receiving an event to be passed to recv_event()

* *int sendOutEvent(std::string event, TrodesMsg &msg)*
    * event: name of your event
    * msg: reference to TrodesMsg, which should contain your event's information

* *void sendTimeRequest()*
* *void sendTimeRateRequest()*

Example of inheriting and using AbstractModuleClient in C++
```cpp
class BasicModule : public AbstractModuleClient{
    void recv_file_open(std::string filename){
        printf("Opening file %s\n", filename.c_str());
    }
    void recv_file_close(){
        printf("Closing file...\n");
    }
}
//===
//main
BasicModule network("BasicModule", DEFAULT_ADDRESS, DEFAULT_PORT);
if(network.initialize() != 0){
    printf("Network could not successfully initialize!\n");
    exit(-1);
}
network.provideEvent("fakeEvent");

```
Example in Python
```python
import sys
sys.path.append('/home/kevin/spikegadgets/trodes/bin/linux/Libraries')
import trodesnetworkpython as tnp 
import struct

#Define this for breaking loop
stillrunning = True
def stoploop():
    global stillrunning
    stillrunning = False

#Definition of client
class BasicClient(tnp.AbstractModuleClient):
    def recv_quit(self):
        stoploop()

#Connect and initialize
network = BasicClient("BasicPython", tnp.DEFAULT_ADDRESS, tnp.DEFAULT_PORT)
if network.initialize() != 0:
    print("Network could not successfully initialize")
    del network
    quit()
```

##High Frequency Data Classes
These classes handle sending and receiving high frequency data over the network. 

###HighFreqDataType
Simple class that stores information about a high frequency data type. This class should be created and passed to the network client when trying to create and register a new data type. 

* **Name**: Name of data type
* **Origin**: Which client in the network the data comes from
* **DataFormat**: The format of each packet of data that comes in. Currently no standardized format, varies from type to type
* **Size**: The size of each data packet in bytes
* **SocketAddress**: The address any subscriber uses to connect and receive data from

* *AbstractModuleClient::registerHighFreqData(HighFreqDataType)*
    * Call this so that other modules can easily find and subscribe to your data type

###HighFreqPub
Publisher class that broadcasts data over the network to anyone subscribed to it
* *HighFreqPub()*
    * Constructor

* *void initialize()*
    * Initializes publisher by finding and binding to a free address, and creating internal pieces 

* *string getAddress()*
    * Get address of publisher. Used to create HighFreqDataType's

* *void publishData(size, data)*
    * Publishes data of a certain size by first memcpying the data into a message, and then sending out

###High Frequency Subscribers
Classes that subscribes to a data type and deals with the data in a different way. The AbstractModuleClient has built in classes that finds the addresses and creates these subscriber objects for you. 

* *AbstractModuleClient::subscribeHighFreqData(name, origin, buffersize)*
    * Call this function so AbstractModuleClient can find, subscribe to, and create a subscriber object based on the given name and origin. 

####HFSubConsumer
Classic producer-consumer paradigm. As the data comes in, it is stored in a ring buffer, where its size is specified by the user (the producer). The user must, in their own loop, retrieve the data onto their buffer. If the user falls behind, the oldest data will start to get overwritten in the ring buffer.

* *HFSubConsumer(highfreqdatatype, ringbuffersize)*
    * Constructor, takes in the data type and a ringbuffer size. 
* *initialize()*
    * Initializes internals
* *size_t readData(void *dest, size_t size)*
    * dest: buffer for data to be copied to
    * size: size of buffer, and the max bytes of the data to be copied
    * return: returns num of bytes that was copied. If 0, then nothing was copied. Should usually equal size
* *int available(timeout)*
    * Number of available unread packets. 
    * timeout: if no available packets, waits for at most timeout milliseconds before returning
    * return: returns number of packets available to be read
* *int available_blocking()*
    * Number of available unread packets, but blocks indefinitely until one appears
Python Example

```python
#Subscribe to high freq data, lfp data and receive handle to stream object
lfpstream = network.subscribeHighFreqData(tnp.LFPDATA, tnp.TRODES_NETWORK_ID, 25)

#initialize high freq consumer object
lfpstream.initialize()

#allocate buffer
buf = memoryview(bytes(32))
while(stillrunning):
    n = lfpstream.available(1000)
    b = lfpstream.readData(buf, 32)
    if n > 0: 
        struct.unpack* ('h'*16, buf)
    if not stillrunning:
        break
```

####HFSubWorker (Currently only C++)
A subscriber class that, on receiving data, will immediately split it up to n worker threads and have each one call a user provided function on the data. 

* *callback_function* signature: (void *data, size_t bytes, void *args)

* *HFSubWorker(HighFreqDataType, int numthreads, callback_function, void *args)
    * HighFreqDataType: data type to be used for initialization
    * numthreads: number of worker threads to be created for the data
    * callback_function: the function to be called by each worker
    * args: pointer to a struct/class/variable so that each worker can access arguments beyond the provided
* *initialize()*
    * Creates threads and sockets



##NetworkDataType
Base class to create custom POD (plain old data) classes to be sent over the network. Sending classes over the network can be done in two ways: 1) sending each member variable one frame at a time in a TrodesMsg or 2) sending the class all together in one encoded frame in a TrodesMsg. 

###Encoding and decoding
To use this class, you must inherit from NetworkDataType and implement the pure virtual functions encode() and readByteArr(const char\* byteArr)