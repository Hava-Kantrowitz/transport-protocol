# transport-protocol

Hava Kantrowitz and Derek Ng CS3700 Project 3 README

High Level Code Approach

Our code is written in Python and broken up into methods. Our sender reads in a port and an IP address from the command line and data through STDIN, and our receiver reads in a port from the command line. Our sender makes a packet as an array of continuous bytes. The first 16 bits are for the sequence number, a value that starts as 1 and increments in numerical order. The next 16 bits are for the number of packets the receiver should expect to receive. The remaining 1440 bytes in the packet are for data read from STDIN.

We read in all our data, and chop it up into sections of 1440 bytes, put each section into a packet, and blast the receiver with all the packets at once. When we create a packet, we place its sequence number in a dictionary, with the sequence number as key and the packet as the value.

Our receiver listens for packets. Once it starts receiving packets, it unpacks the sequence number, total number of packets, and data, and decodes the data into ascii. It then sends an ACK back to the sender acknowledging the receival of the packet. The ACK is just the sequence number of the packet is received. It also adds the data it receives to a dictionary with the sequence number as the key and the data as the value, and checks if all packets have been received. If all packets have been received, it prints the delivered data to STDOUT and sends 3 FINs to the sender to let it know it received all packets. The FIN is a sequence number of 0. If not all packets have been received it waits for further packets to come in.

The sender listens for ACKs and FINs coming from the receiver. When it receives something, it decodes it from the bytes. If it receives a FIN, which as mentioned before is a sequence number of 0, then it knows the receiver received everything and it exits. Otherwise, it removes the sequence number sent in the ACK from the previously built dictionary.

If this process of receiving and responding to ACKs results in an timeout (OSError), then it retransmits all the packets that have not been ACKed, which will be all the packets that remain in the dictionary. If it retransmits 5 times without receiving any ACKs then it assumes the receiver is offline, the network is too congested to get packets through, or the receiver has received everything but was unable to successfully transmit the FINs, and exits gracefully.
Challenges Faced

We found this assignment to be quite doable and an enjoyable distraction from the election. Our biggest issues were with fixing the starter code. There were multiple issues in the starter code that either prevented it from running, or ran fine but were just weird/dumb design choices (such as the use of json). Figuring out all the places that we needed to fix and improve upon within the starter code was more difficult than handling the adverse network conditions and having a good performance.

Testing Overview

We did not write specific tests for our transport protocol. Instead, we relied primarily upon ensuring we could pass the test cases and performance checks covered in the simulator. To check our work in the interim, we relied on print statements directly to the console, and the log() function to print messages to STDERR.

Sources Consulted

Primary source was the project 4 description
Class notes, especially from the Transport lecture
Various StackOverflow posts to remember embarrassing lapses in syntax
