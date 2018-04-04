[Wiki Home](Home) > [Developer Documentation](DevDocumentation) > [Networking](Networking)

# Contents

* [Event System](#markdown-header-event-system)

-----------

# Event System

* [Setup](#markdown-header-setup)
    * [Sending Events](#markdown-header-sending-events)
    * [Receiving Events](#markdown-header-receiving-events)
        * [Initializing Event Handler](#markdown-header-initializing-event-handler)
        * [Enabling the EventHandler Menu](#markdown-header-enabling-the-eventhandler-menu)
* [Using the Event Menu](#markdown-header-using-the-event-menu)
    * [Adding a New Connection](#markdown-header-adding-a-new-connection)
    * [Removing a Connection](#markdown-header-removing-a-connection)
* [Known Issues](#markdown-header-known-issues)

The Trodes Event System is a communication protocol set up through the Trodes Module Network to allow modules to dynamically create and broadcast string events.  At runtime, modules utilizing the Event Handler class can detect and subscribe to available events.  Once subscribed to an event, that module’s Event Handler will receive the same event every time it occurs in the event’s module of origin.  Furthermore, the Event Handler primary function is to link received event occurrences to a list of available actions (basically methods from the listening module that are exposed to Event Handler).

## Setup

Required source files:

* configuration.cpp/.h
* trodesSocket.cpp/.h
* trodesdatastructures.cpp/.h
* eventHandler.cpp/.h

Make sure to add all of the above source and header files to your module’s .pro file under the SOURCES and HEADERS sections respectively.

In your code’s header files, include trodesSocket.h and eventHandler.h. 

Additionally, the QT libraries require all user-defined datatypes to be registered as a QMetaType for them to be transfered via-signals/slots.  Before using the event system, you must add the following lines to your main.cpp file after initializing the QApplication and before initializing the MainWindow:

     qRegisterMetaType<TrodesEvent>("TrodesEvent");  
     qRegisterMetaType<TrodesEventMessage>("TrodesEventMessage");  
     qRegisterMetaType<QVector<TrodesEvent> >("QVector<TrodesEvent>");  
     qRegisterMetaType<uint32_t>("uint32_t");  

### Sending Events

By default all event communication is already setup in trodesSocket.cpp/.h, the only thing you need to do is create the necessary interface connections between your module’s TrodesModuleNetwork object.  Creating and sending events is done primarily via the following three TrodesModuleNetwork calls:

#### `TrodesModuleNetwork::sendEvent(uint32_t curTime, TrodesEventMessage event)`

>Sends an event to all listening modules.  If the event does not currently exist in the master event list, it will append itself to it.  
>You must send events using the TrodesEventMessage class.  
>Used primarily when an event occurs.
>
>Example binding:
>
>     void myModuleClass::sendEventBinding(TrodesEventMessage event) {
>         myModuleNet->sendEvent(currentTime, event);
>     }

__TrodesModuleNetwork::sendNewEventNameRequest(QString eventName)__
>Adds the event to the master event list if it doesn’t exist already.
>Useful to define events so other modules can subscribe to them before they occur
>
>Example binding:
>
>     void myModuleClass::sendNewEventNameRequestBinding(QString event) {
>        myModuleNet->sendNewEventNameRequest(event);
>     }


__TrodesModuleNetwork::sendEventRemoveRequest(QString eventName)__
>Requests that the specified event be removed from the master event list, will do nothing if the event does not exist.
>
>Example binding:
>
>     void myModuleClass::sendEventRemoveRequestBinding(QString event) {
>         myModuleNet->sendEventRemoveRequest(event);
    }

Practically speaking, it is very useful to create a binding slot in your module for each of these calls.  This allows you to connect signals from multiple views across your module to the event system.

###Receiving Events

The EventHandler class provides a GUI implementation that allows your module to not only receive events, but also create connections between those events and your module’s methods.  To do this, an EventHandler object must be initialized (it is recommended that the EventHandler object be a private member of your MainWindow class), and your module’s methods exposed to EventHandler.

####Initializing Event Handler

1. Declare a new EventHandler via the following call:

        new EventHandler(my_ActionList, this);
    * __Note that you must provide the event handler with a QList<String> my_ActionList of strings that list the names of all methods you want to expose to the EventHandler (basically, the methods you want to be able to connect events to).__

2. Connect the EventHandler to your module’s TrodesModuleNetwork via the following call:
 
        my_EventHandler->setUpConnections(my_TrodesModuleNetwork);
    * __Note that my_EventHandler is a ptr to your event handler and my_trodesModuleNetwork is a ptr to your module’s TrodesModuleNetwork object.__

3. Create a slot method to call all the methods that you exposed to the EventHandler in step 1.  
    * Action List Example: 

             void myModuleClass::iniActionList(void) {
                 my_ActionList.append("Function1"); //function at index 0
                 my_ActionList.append("Function2"); //function at index 1
                 my_ActionList.append("Function3"); //function at index 2
             }

    * CallModule() Example:

             void myModuleClass::my_CallMethodSlot(int actionIndex, TrodesEvent event) {
                 switch (actionIndex) { 
                 case 0: {  //Call function at the corresponding index in my_ActionList
                     function1();
                     break;
                 }
                 case 1: {
                     function2();
                     break;
                 }
                 case 2: {
                     function3();
                     break;
                 }
                 }
             }

4. Connect the slot created in step 3 to the EventHandler.

        connect(my_EventHandler, SIGNAL(sig_executeAction(int,TrodesEvent)), this, SLOT(my_CallMethodSlot(int,TrodesEvent)));

####Enabling the EventHandler Menu

By default, EventHandler implements an event menu GUI to manage connections between events.  To enable the menu you must connect the following slot to one of your module’s GUI signals:

__void EventHandler::promptEventConnectionMenu(void)__
>This function sets the main event dialog to visible.
>
>__ Note: It is convenient to create a binding slot for this function in your module and call it by directly accessing it.__

##Using the Event Menu

When you open the EventHandler dialog menu for the first time you will be greeted by the ‘Event Connections” window, it should be empty.  

###Adding a New Connection

1. Click the ‘Add’ button or press the enter key.

    * This brings up the ‘Event/Action Menu’ window.  In the left box are all the events currently visible to your module (this list will be updated automatically when new events are created).  The right box contains your module’s exposed methods.

2. Select one of the events from the list in the ‘Visible Events’ box and then select the action you want to connect that event to from the ‘Available Actions’ box.  Click ‘Ok’ or press the enter key to create the connection.

3. The ‘Event/Action Menu’ window should close and the newly created connection should now be visible in the ‘Event Connections’ window.

###Removing a Connection

To remove a currently active connection, simply select the the connection you want to remove in the ‘Event Connections’ window, and press either the ‘Remove’ button or the delete key on your keyboard.

##Known Issues

* If you press either the ‘Remove’ button or the delete key without first selecting a connection in the ‘Event Connections’ window, the topmost connection will be deleted.

-----------