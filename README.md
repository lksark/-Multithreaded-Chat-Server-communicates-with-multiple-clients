# Multithreaded Chat Server communicates with multiple clients concurrently
 In this TCP chat server & clients example, I am using shared-Stack to distribute workloads of a multithreaded Chat-Server. The Chat-Server should able to accept multiple chat-clients TCP connections concurrently.

 The example code below demonstrates how it works.

Single multithreaded TCP server can maintain communications and duplex data transmission with multiple clients concurrently using multiple ports.

  1. Multithreaded TCP chat server declares 1 thread to run ‘Portal TCP Server’ on port 50000; four threads to run ‘Worker TCP Server’ on port ‘50001~50004’. All threads listening to different TCP ports.

  2. All TCP chat clients who want to establish TCP connection with chat server firstly go to ‘Portal TCP Server’ on port 50000. ‘Portal TCP Server’ will informs chat clients which available TCP ports to connect by using shared workloads-stack.

  3. Chat client establish TCP connection with the available ‘Worker TCP Server’ on assigned port.

  4. If chat client did not establish TCP connection with the ‘Worker TCP Server’ on assigned port within preset timeout period, the port number will be pushed back into shared workloads-stack. The assigned ‘Worker TCP Server’s handshake passcode will be changed too.

  5. To avoid stray chat-clients connect to ‘Worker TCP Server’ directly without firstly visit ‘Portal TCP Server’.

    a. chat-client should get handshake passcode from ‘Portal TCP Server’.

    b. Afterward, chat-client connect to the assigned port of ‘Worker TCP Server’ and send the handshake passcode to maintain connection.

    c. If ‘Worker TCP Server’ did not get the correct handshake passcode in the first string sent by client, ‘Worker TCP Server’ closes the TCP connection. Worker server will change to new handshake passcode. Client only has one chance sends the correct handshake passcode, client will not able to attempt second time. Client should visit ‘Portal TCP Server' again to re-attempt connection.
    
    d. After ‘Worker TCP Server’ close a connection, either closed normally or closed with exceptional error, or closed because client sent a wrong handshake passcode, ‘Worker TCP Server’ will change the handshake passcode. Hence, client will not able to connect to the same ‘Worker TCP Server’ with the previously received handshake passcode, or by multiple passcodes trial-and-error method.

  6. After chat-client established TCP connection with the assigned ‘Worker TCP Server’. If chat-client idle and do not send message to ‘Worker TCP Server’ for a preset timeout period, the TCP connection will be closed. ‘Worker TCP Server’ send string message "SERVER>>> IDLE TIMEOUT" to inform chat-client about connection timeout. ‘Worker TCP Server’ push it port number back to shared workloads-stack, ready to listen new incoming TCP connection.
     Optionally we can preset maximum total connected time of client to ‘Worker TCP Server’. After maximum total connected time, ‘Worker TCP Server’ will close the connection with client by sending string message “SERVER>>> TOTAL TIME TIMEOUT” to client. Even though client remain active during the period.

  7. If all ‘Worker TCP Server’ are currently occupied, ‘Portal TCP Server’ send string message "SERVER>>> FULL" to the new chat-client. New chat-client should sleep for 2 seconds before re-attempt to inquire ‘Portal TCP Server’.

  8. Assume ‘class_currTimeDate’ is a RTC that trigger interrupt in 1 second or 2 seconds frequency.

  9. When chat-client wants to terminate the connection, it sends string message "CLIENT>>> TERMINATE" to chat-server.

In the below example code, port number ‘50001’ chat-client will chat with port number ‘50002’ chat-client; port number ‘50003’ chat-client will chat with port number ‘50004’ chat-client.

Contrast to the below example, client can tell server to establish connection with client assigned port. That's means client at initial connection inform server which client IP & port should server connect to, then close the connection. Then client listen to the pre-determine port, waiting for server to establish connection.

c# multithreaded TCP chat-server Console App:  [TCP_Chat_Server_Console.cs.txt](https://github.com/lksark/-Multithreaded-Chat-Server-communicates-with-multiple-clients/blob/main/TCP_Chat_Server_Console.cs.txt)

c# TCP chat-client Windows Forms App: TCP_Chat_Client.zip 
[TCP_Chat_Client.zip](https://github.com/lksark/-Multithreaded-Chat-Server-communicates-with-multiple-clients/blob/main/TCP_Chat_Client.zip)
