## What is routing?
- The routers jobis to send a packet to the correct destination. Thats where routing comes in
- **Routing** is the process that routers use to determine the path that IP packets shoukld take over a network to reache their destination
	- Routers store routes to all of their known desitnations in a **routing table**
	- when routers recieve packets, they look in the **routing table** to find the best route to forward that packet.
- There are two main routing methods (methods that routers use to learn routes):
	- **Dynamic Routing**: Routers use dynamic routing protocols (like OSPF) to share routing information with each other automatically and build their routing tables
	- we will cover this later
	- **Static Routing:** In this case, a network engineer or admin manually configures routes on a router
- A **route** tells the router: *to send a packet to X, you should send the packet to next-hop Y.*
	- or, if the destination is connected directly to the router, send the packet directly to the destination
	- or, if the destination is the routers own IP address, recieve the packet for yourself
### The network for this lecture
![[Screenshot_2.png]]
- There are 4 routers connected together, this represents a **WAN**
- there are 2 switches connected to PC's, this represents a **LAN**
	- for less visual confusion, only 1 host is in each LAN
- In the next lesson, we will configure **static routes** to allow PC1 and PC4 to communicate with each other
	- This lesson will focus on two types of routes automatically added to the routers routing table
### R1 Pre-configurations
R1 **int G0/0** was configured with **192.168.13.1 255.255.255.0** and enabled with **no shut**
R1 **int G0/1** was configured with **192.168.12.1 255.255.255.0**
R1 **int G0/2** was configured with **192.168.1.1 255.255.255.0**
changes were confirmed with **sho ip int br**

The other routers were configured similarly
## The routing table
Lets look at R1's routing table.
![[Pasted image 20230309114241.png]]
use **show ip route** to see the routing table. The **codes** section shows a legend of codes used to id routes.
- take note of **C** and **L** codes.
- note that R1 already has 6 routes, even without configuration
- When you configure an interface and enable it, 2 routes are already added.
### Connected and Local routes
- a **connected** route is a route to the network the interface is connected to.
- R1 G0/2 = 192.168.1.1/24
- The **connected** route is 192.168.1.0/24. This is the **network address**. it provides a route to all hosts in this network.
	- R1 knows "If I need to send a packet to anything in this network, I send it to G0/2"
- The **local** route is a route to the exact IP address configured on the interface. 
	- the /32 netmask specifies the exact IP address of the interface
		- /32 means all 32 bts are **fixed**, they can't change
	- Even though R1's G0/2 is configured as 192.168.1.1/24, the route is to 192.168.1.1/32
	- R1 knows "If i receive a packet destined for this IP, it is for me"
#### Netmasks 
to make it a bit more clear:
192.168.1.0/24 255.255.255.0
the **/24** means that the first 24 bits of the netmask are all 1, they are fixed and can't change. The last 8 can change. This means that **192.168.1.0/24 = 192.168.1.0-192.168.1.255**

192.168.1.1/32 255.255.255.255
the **/32** means all the bits are 1, they are all fixed. This means **192.168.1.1/32 is only equal to itself**
## Routing fundamentals (Route selection)
So, int G0/2 has a connected route of 192.168.1.0/24 and a local route of 192.168.1.1/32.
- Lets imagine that a packet is sent to R1 with a destination IP of 192.168.1.1. We have a problem, this packet is matched by both routes. in all cases, a router will choose the **most specific** matching route
- in this case, there is a route which **only** contains this IP address, which is more specific, and is therefore selected. 
- most specific matching route = the **matching route** with the **longest prefix length**

Lets take another look at the output of **sh ip route**
You may have wondered what the top sections above **C** and **L** mean. 
**192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks**
- these lines are **not** routes
- in the routing table, there are two routes to **subnets** that fit within the 192.168.1.0/24 class C network, with two different netmasks (/24 and /32)
- the others mean the same thing for their respective networks. We will cover subnetting in another lecture.