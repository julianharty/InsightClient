InsightClient
=============

Insight is a measurement toolkit that allows mobile application developers to collect application analytics and understand  various aspects about the usage of their applications. Insight allows application developers to collect a diverse set of statistics from their application: device type information, client sessions, client location, CPU + memory consumption statistics, battery drain statistics, application specific events.

Furthermore, Insight performs light-weight network latency measurements during the application sessions. This is useful for correlating the network performance with the aforementioned data. For example, by tracking in-app purchases made by users and the network quality statistics of sessions in which these purchases were made, developers can infer how user revenue may be impacted by quality across different networks. 

A version of this toolkit was used for the measurements in our CoNEXT 2013 paper: Capturing mobile experience in the wild: a tale of two apps: http://dl.acm.org/citation.cfm?id=2535391

This repository contains the source code for the client-side Insight implementation. To use Insight to collect statistics from their deployed applications, developers only need to add a few lines of code to their applications. Before using the Insight client, developers need to setup the Insight servers to collect and store data from the Insight clients. The server code and instructions are available at the following locations:-

a. The main Insight server to collect the aforementioned statistics from the clients: https://github.com/patroashish/InsightMainServer

b. The ping measurement server performs lightweight network latency measurements over UDP and TCP to measure network latency between the server and the application (over cellular/WiFi): https://github.com/patroashish/InsightPingServer

To use Insight with your application, include the Insight client code as a library (jar file) or copy the source code directly into the application. To package Insight client code as a jar file, export the "com/wisc/insightlib/" directory in the src/ folder. Then, add the following line of code to your android application:

1. In the onStart() method, add the following 3 lines of code at beginning of the function (after super.onStart()) with specified changes. This code instantiates a new Insight session when the application is started.

		super.onStart();
		
		InsightLib.setServerHostname("example.com"); // Update the server hostname with your Insight server's hostname.
		InsightLib.setApplicationCharID("testUser"); // Update with user ID the application specific user ID information.
		InsightLib.startSession(this.getApplicationContext());
		
		// Other application code.
		
2. In the onStop() method, add the 'InsightLib.endSession()' call at beginning of the function (after super.onStop()). This code ends the existing Insight session when the application is stopped.

		super.onStop();
		
		InsightLib.endSession();
		
		// Other application code.

3. The following methods can be used to log application specific events using Insight. The Insight client code relays this information to the server to be logged for future analytics.

	a. Logs an event denoted by the eventID. It increments the counter for the input event (starting at 0).
		
		InsightLib.captureEvent(Integer eventID);
	
	b. Logs an event corresponding to the eventID and the value corresponding to the event instance.
     
		InsightLib.captureEventValue(Integer eventID, Double value);
	
 	c. Logs an event corresponding to the eventID and the value string corresponding to the event instance.
		
		InsightLib.captureEventString(Integer eventID, String value);
  	 
  	d. The following pair of calls should be wrapped around a download event (e.g., downloading an image file,   transmitting data to a server etc.). These call record the duration and the bytes transferred during the download event.
  
		// Call the method before the start of the download.
		long downloadId = InsightLib.downloadStarted();

		/*
		 * Perform the download activity.
		*/
			
		// Call the method after the end of the download.
		InsightLib.downloadEnded(downloadId);

