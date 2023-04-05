## Native VLAN on a router
- There are **2 methods** of configuring the native VLAN on a router
	- use the command **encapsulation dot1q vlan-id native** on a router subinterface
	- configure the IP address for the native VLAN on the routers physical interface (the **encapsulation command** is not necessary in this case)
## Layer 3 Switching/Multilayer Switching
- in addition to layer 2 switches, there are also **multilayer switches**, also known as **layer 3 switches**
- a multilayer switch is capable of both **switching and routing**
- you can assign IP addresses to switch interfaces, like a router
- with a layer 3 switch, you can configure routes for interfaces, like a router
- you can create virtual interfaces for each VLAN, and assign IP addresses to those interfaces
- it can be used for inter-VLAN routing
### SVI (Switch Virtual Interfaces)
- SVI's are the virtual interfaces you can assign IP addresses to in a multilayer switch
- Configure each PC to use the SVI (NOT the router) as their gateway address
- To send traffic to different subnets or vlans, the client will send traffic to the switch, and the switch will route the traffic
### Configurations
on the router:
- delete sub interfaces with **no interface g0/0/.10**
- set the interface back to its default configuration with **default interface g0/0**
on the switch:
- use **default interface g0/1** to reset the interface to its default configuration
- important command! use **ip routing** to enable layer 3 routing on the switch
- select the interface **int g0/1**
- set the interface as a routed port instead of a switch port **no switchport**
- configure the IP address **ip address 192.168.1.193 255.255.255.252**
- add the route **ip route 0.0.0.0 0.0.0.0 192.168.1.194**
- use **sh ip route** to confirm
- you can also confirm with **sh int stat**
To configure SVI's
- use **interface VLAN10** to create an interface for vlan10
- set the ip address **ip address 192.168.1.62 255.255.255.192**
- activate the interface **no shutdown**
- **remember that all vlans have to exist on the switch**
	- create the vlan you are trying to activate if it doesn't work
	- SVI's will not automatically create the VLANS
	- the vlan must have at least **one access port** or **one allowed trunk port** to be active.
	- the **vlan must not be shutdown** (you can use **shutdown** to disable a vlan)
	- make sure you use **no shutdown** as SVI's are disabled by default