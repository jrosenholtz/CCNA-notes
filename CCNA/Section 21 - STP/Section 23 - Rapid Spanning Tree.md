## Standard vs Cisco STP
Below are some comparisons of cisco proprietary vs standard STP protocols. 
| Spanning Tree Protocol (802.1D)                              | Per-VLAN spanning Tree Plus (PVST+)                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| the original STP                                             | Ciscos upgrade                                               |
| All VLANS share one STP instance                             | Each VLAN has its own STP instance                           |
| Cannot Load balance                                          | Can load balance by blocking different ports in each vlan    |
| can take up to 50 seconds to respond to changes in a network | can take up to 50 seconds to respond to changes in a network | 
In modern networks, 50 seconds is an unnacceptable amount of time, so RSTP was developed.
| Rapid Spanning Tree Protocol (802.1w)                             | Rapid Per-VLAN Spanning Tree Plus (Rapid PVST+) |
| ----------------------------------------------------------------- | ----------------------------------------------- |
| Much faster at converging/adapting to network changes than 802.1D | Ciscos upgrade to 802.1w                        |
| All VLANS share one STP instance                                  | Each VLAN has its own STP instance              |
| it cannot load balance                                            | Can load balance by blocking different ports in each vlan                                                |
Finally, a new protocol was introduced:
| Multiple Spanning Tree Protocol (802.1s)                                                                                            |     |
| ----------------------------------------------------------------------------------------------------------------------------------- | --- |
| Uses Modified RSTP mechanics                                                                                                        |     |
| can group multiple VLANS into different instances (ie. VLANS 1-5 in instance 1, VLANS 6-10 in instance 2) to perform load balancing |     |
| finally a standard STP protocol that handles VLANS!                                                                                 |     |
| actually this is superior to Rapid PVST+                                                                                            |     |
Cisco has not actually made a competitor to MSTP, they simply use the industry standard
## Rapid PVST+
### Ciscos Summary
"RSTP is not a timer-based spanning tree algorithm like 802.1D. Therefore, RSTP offers an improvement over the 30 seconds or more that 802.1D takes to move a link to forwarding. The heart of the protocol is a new bridge-bridge handshake mechanism, which allows ports to move directly to forwarding"
### Similarities between STP and RSTP
- RSTP and STP serve the same purpose, blocking layer 2 loops
- RSTP elects a root bridge with the same rules as STP
- RSTP elects root ports with the same rules as STP
- RSTP elects designated ports with the same rules as STP
### Differences between STP and RSTP
#### Port States
consider the following graph:
| STP port state | Send/Recieve BPDU | Frame Forwarding (regular traffic) | MAC address learning | Stable/transitional |
| -------------- | ----------------- | ---------------------------------- | -------------------- | ------------------- |
| Blocking       | NO/YES            | NO                                 | NO                   | Stable              |
| Listening      | YES/YES           | NO                                 | NO                   | Transitional        |
| Learning       | YES/YES           | NO                                 | YES                  | Transitional        |
| Forwarding     | YES/YES           | YES                                | YES                  | Stable              |
| Disabled       | NO/NO             | NO                                 | NO                   | Stable                    |

This is the port states of standard STP. RSTP condenses these port states into just 3. The **blocking and disabled states are combined**, and the **listening state is no longer used.**
This is the new port states of RSTP:
| STP port state | Send/Recieve BPDU | Frame Forwarding (regular traffic) | MAC address learning | Stable/transitional |
| -------------- | ----------------- | ---------------------------------- | -------------------- | ------------------- |
| Discarding     | NO/YES            | NO                                 | NO                   | Stable                    |
| Learning       | YES/YES           | NO                                 | YES                  | Transitional        |
| Forwarding     | YES/YES           | YES                                | YES                  | Stable              |
- if a port is administratively disabled (**shutdown** command) = discarding state
- if a port is enabled but blocking traffic to prevent layer 2 loops = discarding state
#### Port Roles
- The **root port** role remains unchanged in RSTP
	- the port closest to the root bridge becomes the root port for the switch 
	- the root bridge is the only switch without a root port
- the **designated port** role remains unchanged in RSTP
	- the port on a segment (collision domain) that sends the best BPDU is that segments designated port (only one per segment)
- the **non-designated port** role is split into two seperate roles in RSTP:
##### Alternate Port Role
- the RSTP **alternate** port role is a discarding port that recieves a superior BPDU from another switch
- this is the same as what you've learned about blocking ports in classic STP
- an alternate port functions as a backup to the root port
- if the root port failes, the switch can immediately move its best alternate port to forwarding
- this functions similarly to a non-designated port
- this immediate move to forwarding state functions like a classic STP optional feature called UplinkFast. it is not needed in RSTP
##### Backup Port Role
- the RSTP **backup** port role is a discarding port that receives a superior BPDU from another interface on the same switch
- this only happens when two interfaces are connected to the same collision domain (via a hub)
- hubs are not used in modern networks, so you will probably not encounter and RSTP backup port
- function as a backup for a designated port
- the interface with the lowest port ID will be selected as the designated port
## Other Considerations
- Rapid STP **is** compatible with classic STP, the switch running RSTP will simply use the STP settings, such as hello timer and max age
- All switches in RSTP send their own BPDU's every hello time (2 sec)
- Switches 'age' the BPDU information more quickly. in classic STP, a switch waits 10 hello intervals (20 sec). in rapid STP, a switch considers a neighbor lost if it misses 3 BPDU's (6 seconds). It will then 'flush' all MAC addresses learned on that interface
### RSTP Link Types
- RSTP distinguishes between three different 'link types'
- **Edge**: a port that is connected to an end host. Moves directly to forwarding, without negotiation
	- this is similar to portfast from STP
	- to enable: **spanning-tree portfast**
- **Point-to-point**: a direct connection between two switches
	- these function in full duplex
	- switches should detect if they are detected to another switch
	- to enable **spanning-tree link-type point-to-point**
- **Shared**: a connection to a hub. Must operate in half-duplex mode.
	- you don't need to configure this, it should be detected
	- to enable: **spanning-tree link-type shared**
### RSTP BPDU
Most of the BPDU is the same, but there are a few differences
![[Pasted image 20230326123644.png]]
- notice the Protocol Version ID is now 2, where on standard STP it is 0
	- **0 for classic, 2 for rapid!**
- the classic STP BPDU uses only 2 bits of the flags, the RSTP uses all 8 bits to allow rapid STP to converge must faster
- in classic STP, only the root bridge originated BPDUs, and other switches forwarded them
	- in RSTP, all switches originate BPDU and send BPDU's from designated ports
