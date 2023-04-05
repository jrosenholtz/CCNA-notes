## OSI Model - PDU's
Before we continue with learning, lets quickly review the OSI model. Remember our pnuemonic **All People Seem To Need Data Processing.**
7. Application
6. Presentation
5. Session
4. Transport
3. Network
2. Data Link
1. Physical
### The PDU Sequence 
1. Data is created
2. Data is encapsualated into a **segment** on **layer 4**
3. Segments are encapsulated into a **packet** on **layer 3**
4. packets are encapsulated into a **frame** on **layer 2**
5. frames are sent out of the device on **layer 1.**
For this lesson, we are focusing on the **Layer 3 header**

## IPv4 Packet Structure
Lets take another look at the IPv4 Header:
![[Pasted image 20230308133404.png]]
It looks pretty complicated, but in all likelihood you will not need to memorize all the sizes of each field. It is best to be familiar with all fields of the header though. This chart is read left-to-right, so **version** is the first field of the header, and **options** is the last field.
### Fields of the IPv4 Header
#### Version field
- 4 bits
- identifies the version of IP used
- IPv4 = 4 (0100)
- IPv6 = 6 (0110)
#### IHL (Internet Header Length)
- 4 bits
- The final field of the IPV4 header (options) is variable in length, so this field is necessary to indicate the total length of the header
- Identifies the length of the header in **4 byte increments**
- therefore, if the value in this field is 5, the length of the header is 5x4=20 bytes
- the minimum value is 5 (=**20 bytes**)
- The maximum value is 15(=**60 bytes**)
#### DSCP (Differentiated services code point)
- 6 bits
- Used for QOS (quality of service)
- Used to prioritize delay-sensitive data (streaming voice, video, etc)
#### ECN (Explicit Congestion Notification)
- 2 bits
- provides end-to-end (between two endpoints) notification of network congestion **without dropping packets**
- optional feature that requires both endpoints, as well as the underlying network infrastructure to support it
#### Total Length
- 16 bits
- indicates the total length of the packet ( L3 header + L4 segment)
- Measured in bytes (**not 4 byte increments like IHL**)
- Minumum value of **20** (IPv4 header with no data)
- Maximum value of **65,535** (Maximum 16 bit value)
#### Identification Field
- 16 bits
- if a packet is fragmented due to being too large, this field is used to identify which packet the fragment belongs to
- all fragments of the same packet will have their own IPv4 header with the same value in this field.
- packets are fragmented if larger then the **MTU (Maximum Transmission Unit)**
- The MTU is usually **1500 bytes**
- Remember the maximum size of an ethernet frame?
- fragments are reassembled by the recieving host
#### Flags Field
- 3 bits
- used to control/identify fragments
- Bit 0: reserved, always 0
- Bit 1: Don't Fragment (DF bit), used to indicate the packet should not be fragmented
- Bit 2: More Fragments (MF bit), set to 1 if there are more fragments in the packet, set to 0 for last fragment
#### Fragment offset
- 13 bits
- used to indicate the position of the fragment within the original, unfragmented IP packet
- This lets fragments be reassembled, even if they arrive out of order
#### Time to live
- 8 bits
- a router will drop a packet with a TTL of 0
- used to prevent infinite loops
- Originally designed to indicate lifetime in seconds
- in pracice, represents a 'hop count', each time the packet arrives at a router, the router decreases TTL by 1
- recommended is 64
#### Protocol field
- 8 bits
- indicates the protocol of the encapsulated packet
- Value of 6: TCP
- Value of 17: UDP
- Value of 1: ICMP
- Value of 89: OSPF (Dynamic routing protocol)
#### Header Checksum
- Checksum used to check for errors
- when a router recieves a packet, it generates a checksum and compares
- if they do not match, it drops the packet
- **note that this only looks for errors in the header, not the data**
- TCP and UDP have their own headers to check for errors in the data
#### Source/Destination IP address fields
- 32 bits
- Source IP address = IP of sender
- Destination IP address = IP of reciever
#### Options
- 0 - 320 bits
- Rarely used
- if the IHL field is greater than 5, it means that options are present. 
- Not necessary for CCNA