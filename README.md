# MODBUSRTU2MQTT

This a python program to read data from modbus rtu devices and then publish them to mqtt topics. Configuration can be set by only editing JSON files.

What you need to know:
1. I assume you: 
	- have a basic knowledge about Modbus RTU Communication (if you are a beginner, read this http://www.simplymodbus.ca/FAQ.htm)
	- use python3.6 or later.

   Python modules needed:
	- minimalmodbus
	- numpy
	- MySQLdb
	- paho.mqtt
	- pprint
	- psutil

2. This program also requires MySQL database to be installed.
   Name, username, and the password of the database can be set in MyDB.py
   
3. Modbus RTU devices and their registers that want to be read can be configured by editing JSON files in directory JSON/Config

   a. DevicesIdentity.json 
      contains information about devices identity.
      - Name		: Name of the device
      - Address		: modbus device address
      - CommPort	: port used in your pc
	  - Baudrate	: serial communication baudrate
	  - Parity		: serial communication parity ("NONE", "ODD", "EVEN")
	  - ByteSize	: serial communication bytesize
	  - StopBit		: serial communication stopbit
      - ByteOrder	: byte order in the device memory system
      - AccessToken	: mqtt username (for authentication with mqtt broker),
      - Timeout		: timeout limit for the device
	  
	  
   b. MQTTConfig.json 
      contains settings for mqtt communication and polling interval.
      - PollingInterval	: interval of devices data polling 
      - BrokerHOST		: mqtt broker address
      - MQTTPort		: mqtt broker port
      - TopicList		: mqtt topic list. for example: ["v1/devices/me/telemetry", "v1/devices/me/attributes"]
	
	
   c. DeviceVariables.json
      contains list of variables in each device with its OID, Datatype, etc.
      - Name 		: 	name of the variable
      - RelativeRegAddress	: 	relative register address of the variable
	  - RegisterType	: Register Type. ("Discrete Input", "Discrete Output","Holding Register","Input Register")
	  - WordLength		: Number of register used to store the variable value
      - Datatype	: 	Variable data type ("INT16","INT32","INT64","UINT16","UINT32","UINT64","FLOAT16","FLOAT32","FLOAT64","STRING","BIT")
      - Multiplier	: 	Value of this variable will be multiplied with this value (if you need numerical data processing)
      - PublishTopic	: 	mqtt topic index in TopicList. The variable value will be published on this topic.
      
	
	
4. To begin data polling, just run the Poller.py. The program will automatically publish the data to mqtt topic periodically, based on the polling interval. The result can also be seen in JSON/Result


5. Device control via MQTT is still in development. But you can do device control by using Control.py directly.

   Run this command:
   
   python3.6 Control.py --DeviceNo [Device_Number_Index] --VarName [List_of_variable] --Value [List_of_Value]
   
   Device_Number_Index 	==> device index (based on the order in DevicesIdentity)
   
   List_of_variable		==> list of variable that want to be set
   
   List_of_Value		==> list of desired value of each variable
   
   for example:
   
   	python3.6 Control.py --DeviceNo 1 --VarName I_neutral,V_12 --Value 12.23,380.74
 
   this command will set I_neutral to 12.23 and V_12 to 380.74 in the first Device
   
