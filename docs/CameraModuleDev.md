[Wiki Home](Home) > [Developer Documentation](DevDocumentation) > [Camera Module](CameraModuleDev)

#Contents
* [Setup](#markdown-header-setup)
* [Networking](#markdown-header-networking)
---------
#Setup

---------
#Networking
##Streaming Position Data
*Message type definition flag: TRODESDATATYPE_POSITION*

Generally speaking, position data is sent each time a new frame is processed by the camera.  The frequency of position data’s continuous stream is therefor locked with the camera’s current frame-rate, which in this case is thirty frames per second.  Due to software restrictions on the amount of available continuous data streams, all position data is sent dynamically over a single stream (the TRODESDATATYPE_POSITION stream) as one large packet.  

Because it would be wasteful to send all of the data all of the time (for instance, there is no reason to send linear track data more than once or whenever it is edited), the position data packets are dynamically constructed before being sent.  While this is convenient and allows for greater flexibility on the sending side, the dynamic nature of the data packets means that the structure and size of incoming data packets are not guaranteed on the receiving end.  Thus, modules receiving position data must decode the packets manually.  

To help receiving modules decode incoming data, position data packets are constructed by preceding different types of data with flags that describe its’ format.  For example, the integer flag “PPT_2DPos” will always precede two consecutive qint16 variables describing the currently tracked objects x,y pixel position in CameraModule’s graphics view.  The entire position data packet is made up of many of these smaller ‘sub-packets,’ and the specific formats for all of these ‘sub-packets’ are defined in the [Data Packet Format](#markdown-header-data-packet-format) section.  Further, see the [General Data Reading Procedure](#markdown-header-general-data-reading-procedure) section for information on how to decode incoming data packets.

###Available Data
* Message Header (flag: PPT_Header)
    * Contains general information about the module of origin; the timestamp the message was sent, the camera number, and the total number of packets sent.

* Tracked Two Dimensional Pixel Coordinates (flag: PPT_2DPos)
    * Coordinate relative to the pixel dimensions of the graphics window (window streaming video).
    * Is the circled location in black/white setting, and the green circle in the two-LEDs setting.

* Tracked Linear Position (flag: PPT_Lin)
    * Gives the segment ID and the relative location of the on the line segment of the tracked coordinates orthogonal projection on a defined linear track.

* Linear Track Data (flag: PPT_LinTrack)
    * The total number of line segments plus the following data for each segment: the segments number/id

* Zone Dimensions (flag: PPT_Zone)
    * Total number of nodes in each user defined zone plus the following data for each node: the node's coordinates (relative position 0.0-1), and the node's ID.
    * Note that each node is given sequentially, meaning that each node is connected to the one before and after it by a line.  The first and last nodes are also connected.

* Python-Calculated Velocity (flag: PPT_Velocity)
    * Velocity value calculated via embedded python script
    * Defaults to zero if python is uninitialized (inactive or a script not loaded)

__NOTE:  qreal variables are essentially doubles, they share the same memory size__

###Data Packet Format
The following is a list of flags and the data that follows them, note that the incoming flag itself is always an ‘int’:

* “PPT_Header” contains:
    * __(quint32)__ timestamp that the message was sent
    * __(uint8_t)__ number ID of the camera that the position data is from
    * __(int)__ total number of packets sent by the camera, this is a debugging variable

* “PPT_2DPos” contains:
    * __(qint16)__ x positition of the currently tracked object in real pixel coordinates
    * __(qint16)__ y positition of the currently tracked object in real pixel coordinates

* “PPT_Lin” contains:
    * __(qint16)__ line segment of the current linear track that the linear position projection is in
    * __(qreal/double)__ relative position (0.0-1.0 scale) of the linear position projection on the previously received line segment

* “PPT_LinTrack” contains:
    * __(qint16)__ total number of lines in a linear track
    * For each line in the linear track there is:
        * __(qint16)__ the line segment number
        * __(qreal/double)__ relative (0.0-1.0) x coordinate of the line segment’s start node
        * __(qreal/double)__ relative (0.0-1.0) y coordinate of the line segment’s start node
        * __(qreal/double)__ relative (0.0-1.0) x coordinate of the line segment’s end node
        * __(qreal/double)__ relative (0.0-1.0) y coordinate of the line segment’s end node
        * __Note that the coordinates are relative to the graphics window__

* "PPT_Zone" contains:
    * __(uint8_t)__ zone ID
    * __(qint16)__ total number of nodes in the received zone
    * For each node in the zone there is:
        * __(qreal/double)__ relative (0.0-1.0) x coordinate of the node
        * __(qreal/double)__ relative (0.0-1.0) y coordinate of the node

* “PPT_Velocity” contains:
    * __(qreal/double)__ velocity calculated via embedded python

* “PPT_NULL” contains:
    * No data; the null flag marks the end of the data packet

__ Note that each header flag should be read as an ‘int’ and that PPT stands for ‘Position Packet Type’__

###General Data Reading Procedure

1. Start by reading out the first flag in the data packet.
2. Create a loop structure (a while loop is optimal here) to continue reading flags until it reads the “PPT_NULL” flag.
3. Inside the loop structure, create a flow-control statement (like a switch statement) to handle each possible received flag and write code to extract the data in the format defined in the Data Packet Format section.
    * __Note that data must be extracted to the properly sized containment variable.__  For example, all timestamps must be extracted to a ‘quint32’ variable in the following fashion: “receivedDataPacket >> timestampVar”.