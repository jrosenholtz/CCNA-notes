## Routing Information Protocol (RIP)
- Routing Information Protocol (industry standard)
- Distance Vector IGP
	- this means it uses routing-by-rumor logic to learn/share routes
- uses hop count as its metric. one router = one hop (bandwith is irrelevant)
- the maximum hop count is **15** (anything more is considered unreachable)
- Three versions
	- RIPv1 and RIPv2 used for IPv4
	- RIPng (RIP next gen) used for IPv6
- uses two message types:
	- **request:** asks RIP-enabled neighbor routers to send a routing table
	- **Response:** sends a local routers table to neighboring routers
- by default, RIP enabled routers share tables every 30 seconds
### RIPv1 and RIPv2
#### RIPv1
- only advertises **classful** addresses (A, B, and C)
- because it only supports classful addresses, it doesnt support VLSM or CIDR
- doesn't include subnet mask ingormation in advertisments (response messages)
	- 10.1.1.0/24 would become 10.0.0.0
	- 172.16.192.0/18 would become 172.16.0.0
	- 192.168.1.4/30 would become 192.168.1.0
- messages are broadcast to 255.255.255.255
#### RIPv2
- supports VLSM and CIDR
- includes subnet mask info in advertisements
- messsages are **multicast** to 224.0.0.9
	- multicast messages are only sent to specific devices that have joined a multicast group
### RIP configuration
- enter RIP config mode with **router rip**
- select the version with **version 2**
- turn off auto summary to ensure networks are not converted to classful networks **no auto-summary**
- use the network command to tell the router to look for interfaces with an IP address in a specified range. This will activate RIP on interfaces in that range
	- **network 10.0.0.0**
- if you have an interface that is not connected to any other routers, advertising is wasted bandwith, so it should be configured as a **passive interface**
- use the command **passive-interface g2/0** to set the interface as passive. Note that this is done from the rip configuration mode
- EIGRP and OSPF have this same functionality
- if you want to share the default route over RIP, use **default-information originate** from rip configuration mode
- use **show ip protocols** to see the routing protocols being used
## Enhanced Interior Gateway Routing Protocol (EIGRP)
- was cisco proprietary, but cisco has now published it openly so other vendors can implement it on their equipment
- considered an "advanced" or "hybrid" distance vector routing protocol
- much faster then rip in reacting to changes in the network
- does not have the 15 "hop count" limit
- sends messages using multicast address 224.0.0.10
- the only IGP that can perform **unequal** cost load balancing (by default i performs ECMP load-balancing over 4 paths like RIP)
### EIGRP Configuration
- enter eigrp configuration mode with **router eigrp 1**
	- the 1 represents an autonomous system number. All routers need the same AS number
- turn off auto-summary **no auto-summary**
- configure passive interfaces **passive interface g2/0**
- set the network **network 10.0.0.0**
- if you want to set the network with a netmask, you can use **network 10.0.0.0 0.0.0.15**
Wait, whats with this 0.0.0.15?
#### Wildcard Masks
- wildcard masks are like inverted subnet masks.
- All 1's in the subnet mask are 0 in the equivelant wildcard mask. All 0s in the subnet mask are 1 in the equivelant wildcard mask.
	- 255.255.255.0 --> 0.0.0.255
	- 255.255.0.0 --> 0.0.255.255
	- 255.0..0.0 --> 0.255.255.255
- a 0 in the wildcard mask means the bits **must match** between the IP address and the other IP's in the network. Exactly the opposite of regular netmasks
#### Router ID
- Routers use Router-ID's to identify themselves to other routers in the EIGRP network
- Router ID is determined like this
	- if it is manually set, that will be the router id
	- the router ID will be the highest IP address on a loopback interface
	- if there are no loopback interfaces, it will be the highest IP address on a physical interface
- Even though the Router-ID looks like an IP address, its actually just a 32 bit number formatted like an IP address.
#### Unequal cost load balancing
- Variance 1 = only equal cost multipath (ECMP) will be performed
- from eigrp configuration mode, use the **variance num** command. 
- Variance 2 means that the feasible successor route with a Feasible distance of up to 2x the successors FD can be used
## Loopback Addresses
to configure a loopback interface:
- **interface loopback num**
- now you can select it with **int** and configure it as a normal interface.
- add an IP address **ip address 1.1.1.1 255.255.255.255**
	- it is common to use a /32 mask for loopback addresses
- a loopback interface is **always up**
## EIGRP Terminology
- Feasible Distance = the routers metric value to the routes destination
- Reported Distance (Advertised Distance) = the neighbors metric value to the routs destination
- successor = the route with the lowest metric to the destination
- feasible successor = an alternat route to the destination which meats the feasability condition
- feasability condition = a route is considered a feasible successor if its reported distance is lower than the successor routes feasible distance