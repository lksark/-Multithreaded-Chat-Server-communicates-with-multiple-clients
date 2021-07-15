# Multithreaded Chat Server communicates with multiple clients concurrently
 In this chat server & chat clients example, I am using shared-Stack to distribute workloads of a multithreaded Chat-Server application. The Chat-Server application should able to accept multiple chat-clients network connections concurrently.

 The example code below demonstrates how it works.

 Single multithreaded server application can maintain communications and duplex data transmission with multiple clients concurrently using multiple ports.

 1. Multithreaded chat server application declares 1 thread to run ‘Portal Server’ using [UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol) / [TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) [transport layer protocol](https://en.wikipedia.org/wiki/Transport_layer) on port 50000; four threads to run ‘Worker Server’ using TCP transport layer protocol on port ‘50001~50004’. All threads listening to different network ports.

 2.  All chat clients who want to establish network connection with chat server firstly go to ‘Portal Server’ on port 50000 using UDP / TCP transport layer protocol. ‘Portal Server’ will inform chat clients which available worker servers’ network port to connect by using shared workloads-stack.

 3. Chat client establish connection with the available ‘Worker Server’ on assigned port using TCP transport layer protocol.

 4. If chat client did not establish connection with the ‘Worker Server’ on assigned port within preset timeout period, the port number will be pushed back into shared workloads-stack. The assigned ‘Worker Server’s handshake passcode will be changed too.

 5. To avoid stray chat-clients connect to ‘Worker Server’ directly without firstly visit ‘Portal Server’.

     a. chat-client should get handshake passcode from ‘Portal Server’.

     b. Afterward, chat-client connect to the assigned port of ‘Worker Server’ and send the handshake passcode to maintain connection.

     c. If ‘Worker Server’ did not get the correct handshake passcode in the first string sent by client, ‘Worker Server’ closes the network connection. Worker server will change to new handshake passcode. Client only has one chance sends the correct handshake passcode. Client will not able to attempt second time. Client should visit ‘Portal Server' again to re-attempt connection.
    
     d. After ‘Worker Server’ close a connection, either closed normally or closed with exceptional error, or closed because client sent a wrong handshake passcode, ‘Worker Server’ will change the handshake passcode. Hence, client will not able to connect to the same ‘Worker Server’ with the previously received handshake passcode, or by multiple passcodes trial-and-error method.

  6. After chat-client established network connection with the assigned ‘Worker Server’. If chat-client idle and do not send message to ‘Worker Server’ for a preset timeout period, the connection will be closed. ‘Worker Server’ send string message "SERVER>>> IDLE TIMEOUT" to inform chat-client about idle connection timeout. ‘Worker Server’ push it port number back to shared workloads-stack, ready to listen new incoming connection.
     Optionally we can preset maximum total connected time of client to ‘Worker Server’. After maximum total connected time, ‘Worker Server’ will close the connection with client by sending string message “SERVER>>> TOTAL TIME TIMEOUT” to client. Even though client remain active during the period.

  7. If all ‘Worker Server’ are currently occupied, ‘Portal Server’ send string message "SERVER>>> FULL" to the new chat-client. New chat-client should sleep for 2 seconds before re-attempt to inquire ‘Portal Server’.

  8. Assume ‘class_currTimeDate’ is a RTC that trigger interrupt in 1 second or 2 seconds frequency.

  9. When chat-client wants to terminate the connection, it sends string message "CLIENT>>> TERMINATE" to chat-server.

In the below example code, port number ‘50001’ chat-client will chat with port number ‘50002’ chat-client; port number ‘50003’ chat-client will chat with port number ‘50004’ chat-client.

Contrast to the below example, client can tell server to establish connection with client assigned port. That's means client at initial connection inform server which client IP & port should server connect to, then close the connection. Then client listen to the pre-determine port, waiting for server to establish connection.
#
#### Revision '0':

c# multithreaded chat-server Console App, both portal server & worker servers use TCP:  [TCP_Chat_Server_Console.cs.txt](https://github.com/lksark/-Multithreaded-Chat-Server-communicates-with-multiple-clients/blob/main/TCP_Chat_Server_Console.cs.txt)

c# chat-client Windows Forms App, only TCP: [TCP_Chat_Client.zip](https://github.com/lksark/-Multithreaded-Chat-Server-communicates-with-multiple-clients/blob/main/TCP_Chat_Client.zip)
#
#### Revision '1':
   1. Server & client applications use XML to send string & parse received string.
   2. Add timeout check in client program. If server does not respond to client for extended period, client send ‘<CLIENT><command>IDLE TIMEOUT</command></CLIENT>’ message to server and close the connection.

c# multithreaded chat-server Console App, both portal server & worker servers use TCP:  [TCP_Chat_Server_Console_rev1.cs.txt](https://github.com/lksark/-Multithreaded-Chat-Server-communicates-with-multiple-clients/blob/main/TCP_Chat_Server_Console_rev1.cs.txt)

c# chat-client Windows Forms App, only TCP:  [TCP_Chat_Client_rev1.zip](https://github.com/lksark/-Multithreaded-Chat-Server-communicates-with-multiple-clients/blob/main/TCP_Chat_Client_rev1.zip)
#
#### Revision '2':
   1. ‘Portal Server’ listens using UDP transport layer protocol; ‘worker servers’ listen using TCP transport layer protocol.
      ‘Portal Server’ uses User Datagram Protocol (UDP) transport layer protocol, it provides a connectionless datagram service that prioritizes time over reliability. TCP transport layer protocol features three-way handshake, retransmission, and error-detection, adds reliability but lengthens latency.

c# multithreaded chat-server Console App, UDP portal server & TCP worker servers:  [Chat_Server_Console_UDP-TCP.cs.txt](https://github.com/lksark/-Multithreaded-Chat-Server-communicates-with-multiple-clients/blob/main/Chat_Server_Console_UDP-TCP.cs.txt)

c# chat-client Windows Forms App, first UDP then TCP: [Chat_Client_UDP-TCP.zip](https://github.com/lksark/-Multithreaded-Chat-Server-communicates-with-multiple-clients/blob/main/Chat_Client_UDP-TCP.zip)
