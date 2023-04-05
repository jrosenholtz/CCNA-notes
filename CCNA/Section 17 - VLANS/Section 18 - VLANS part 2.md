## Topology
![[Pasted image 20230315155019.png]]
Here is the topology for this section. Note that VLAN10 is split between the switches in this topology. This is very common, as most of time departments aren't conveniently split up. There has to be a link in VLAN 10 between SW1 and SW2 so they can still reach each other. 
Lets imagine the PC is VLAN20 wants to speak with one of the PC's in VLAN10 on SW1
1. It was send a frame with a destination MAC of R1
2. R1 will then send the frame back along the VLAN10 interface
3. SW2 forwards along the VLAN10 interface between itself and SW1
4. SW1 sends the frame to the PC.
## What is a trunk port?
- in a small network with only a few VLANS, it is possible to use a seperate interface for each VLAN when connecting switches to switches, and switches to routers.
- When the number of CLANS increases, this is not viable. This will result in wasted interfaces, and most of the time routers won't have enough interfaces for all the VLANS
- You can use **trunk ports** to carry traffic from multiple VLANS over a single interface
### How does a trunk port work?
![[Pasted image 20230315155525.png]]
We now have configured the line between SW1 and SW2, and the line between SW2 to be a trunk port. this is now a **single physical connection**, but multiple VLANS can travel over it. 
Now, if the port is not set for a specific VLAN, how does the other switch know what VLAN it belongs to? 
the answer is **VLAN tagging**. In fact, sometimes trunk ports are called **tagged** ports, and access ports are called **untagged** ports 

## VLAN tagging
- There are 2 main trunking protocols, ISL (Inter-Switch Link) and IEEE 802.1Q, usually called **dot1q**
- ISL is an old Cisco Proprietary protocol created before the industry standard IEEE 802.1Q
- IEEE 802.1Q is an industry standard protocol created by the IEEE
- you will probably NEVER use ISL in the real world, even modern cisco equipment doesn't support it
### 802.1Q Encapsulation
Lets review an ethernet frame for a moment:
| Eth Header | Packet | Eth Trailer |
| ---------- | ------ | ----------- |
| 20 bytes   |        | 4 bytes     |
When a frame is tagged, a 32 bit field is inserted between src. MAC and Type in the header:
| Preamble | SFD (Start frame delimiter) | Destination | Source  | 802.1q  | Type (or length) |
| -------- | --------------------------- | ----------- | ------- | ------- | ---------------- |
| 7 bytes  | 1 byte                      | 6 bytes     | 6 bytes | 4 bytes | 2 byte           | 
- the tag is 4 bytes (32 bits)
- the tag consists of 2 main fields:
	- Tag Protocol Identifier (TPID)
	- Tag Control Information (TCI)
- The TCI consists of three sub fields:
![[Pasted image 20230315160359.png]]
#### TPID
- 16 bits in length
- always set to a value of 0x8100. This indicates the frame is 802.1Q tagged
#### PCP
- Priority Code Point
- Used for Class of Service (CoS) which prioritizes important traffice
#### DEI
-  Drop eligible Indicator
- 1 bit in length
- indicates whether a packet can be dropped in the case of network congestions
#### VID
- VLAN ID
- 12 bits in length
- Identifies the VLAN the frame belongs to
- 12 bits = 4096 total VLANS (2^12)
- VLANS 0 and 4095 are reserved
- therefore, the range of usable VLANS is 1 - 4094
### VLAN ranges
- The range of VLANS (1 - 4094) is divided into 2 sections
	- Normal VLANS: 1 - 1005
	- Extended VLANS: 1006 - 4094
- Some older devices cannot use the extended range. Its safe to assume modern switches will support the entire range.
### Native VLAN
- 802.1Q has a feature called the **native VLAN** (ISL does not have this feature)
- The Native VLAN is VLAN 1 by default on all trunk ports, however this can be manually configured on each trunk port
- The switch does not add an 802.1Q tag to frames in the native VLAN
- When a switch recieves an untagged frame on a trunk port, it will assume the frame belongs to the native VLAN. Therefore, **it is very important for all switches to have the same native VLAN**
- 
## How to configure trunk ports
- Select interface with **int g0/0**
- enter **switchport mode trunk**
	- if the command is rejected because of trunk encapsulation, move on to the next step
- use **switchport trunk encapsulation dot1q** to choose the encapsulation protocol
- use **sh interfaces trunk** command to view the trunk ports
- To configure the VLANS allowed on a trunk, sue **switchport trunk allowed vlan**
	- **word** allows you to configure allowed VLANS just with plain language
		- **switchport trunk allowed vlan 10,30**
	- **add** appends allowed VLANS
	- **remove** deleted VLANS from allowed list
	- **all** allows all VLANS
	- **except** forbids VLANS from trunk port
	- **none** wipe allowed VLANS from trunk
- To change the native VLAN **switchport trunk native vlan 1001**
	- for security purposes, it is best to change the native VLAN to an **unused VLAN** and make sure native VLANS match
- to view the vlan status use **sh vlan br**
	- this only shows access ports, not trunk ports. use **sh int trunk** to see trunk ports
## Router on a Stick (ROAS)
- it is possible to divide one interface into several seperate sub interfaces without having to use multiple cables
- ROAS is used to route to multiple VLANS on a single interface on the router and switch
- The switch interface is configured as a regular trunk
- the router interface is configured using **subinterfaces**. each sub interface needs its own VLAN tag and IP address
- The router will behave as if frames arriving on a certain VLAN tag have arrives on the subinterface configured with that VLAN tag
- the router will tag frames sent out of each subinterface with the VLAN tag configured on the subinterface
### Configurations
- select the interface **int g0/0**
- make sure the interface is up **no shut**
- enter a sub interface **int g0/0.10**
	- the sub interface number does **not** have to match the VLAN number, but it is **highy recommended** that they do.
- choose encapsulation protocol and VLAN number **encapsulation dot1q 10**
- set IP address **ip address 192.168.1.62 255.255.255.192**
- use **sh ip int br** to view these sub interfaces