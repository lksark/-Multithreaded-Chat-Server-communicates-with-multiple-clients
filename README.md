# Multithreaded Chat Server communicates with multiple clients concurrently
 In this TCP chat server & clients example, I am using shared-Stack to distribute workloads of a multithreaded Chat-Server. The Chat-Server should able to accept multiple chat-clients TCP connections concurrently.
 
 The example code below demonstrates how it works.

  Single multithreaded TCP server communicates with multiple clients using multiple ephemeral ports.

  1. Multithreaded TCP chat server declares 1 thread to run ‘Portal TCP Server’ on port 50000; four threads to run ‘Worker TCP Server’ on port ‘50001~50004’. All threads listening to different TCP ports.

  2. All TCP chat clients who want to establish TCP connection with chat server firstly go to ‘Portal TCP Server’ on port 50000. ‘Portal TCP Server’ will informs chat clients which available TCP ports to connect by using shared workloads-stack.

	3. Chat client establish TCP connection with the available ‘Worker TCP Server’ on assigned port.

	4. If chat client did not establish TCP connection with the ‘Worker TCP Server’ on assigned port within preset timeout period, the port number will be pushed back into shared workloads-stack. The assigned ‘Worker TCP Server’s handshake passcode will be changed too.

	5. To avoid stray chat-clients connect to ‘Worker TCP Server’ directly without firstly visit ‘Portal TCP Server’.

		a. chat-client should get handshake passcode from ‘Portal TCP Server’.

**     Afterward, chat-client connect to the assigned port of ‘Worker TCP Server’ and send the handshake passcode to maintain connection.

**      If ‘Worker TCP Server’ did not get the correct handshake passcode, ‘Worker TCP Server’ closes the TCP connection.

*     After chat-client established TCP connection with the assigned ‘Worker TCP Server’. If chat-client idle and do not send message to ‘Worker TCP Server’ for a preset timeout period, the TCP connection will be closed. ‘Worker TCP Server’ send string message "SERVER>>> TIMEOUT" to inform chat-client about connection timeout. ‘Worker TCP Server’ push it port number back to shared workloads-stack, ready to listen new incoming TCP connection.

*     If all ‘Worker TCP Server’ are currently occupied, ‘Portal TCP Server’ send string message "SERVER>>>full" to the new chat-client. New chat-client should sleep for 2 seconds before re-attempt to inquire ‘Portal TCP Server’.

8.     Assume ‘class_currTimeDate’ is a RTC that trigger interrupt in 1 second or 2 seconds frequency.

9.     When chat-client wants to terminate the connection, it sends string message "CLIENT>>> TERMINATE" to chat-server.

In the below example code, port number ‘50001’ chat-client will chat with port number ‘50002’ chat-client; port number ‘50003’ chat-client will chat with port number ‘50004’ chat-client.

 

c# multithreaded TCP chat-server Console App:  TCP_Chat_Server_Console.cs.txt

c# TCP chat-client Windows Forms App: TCP_Chat_Client.zip 
