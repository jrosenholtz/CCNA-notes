## STP States and Timers
### Spanning Tree Port States
Lets look at the possible states of spanning tree. We already know 2
- **Blocking** - the port will not forward traffic. These ports are **non-designated**
	- these interfaces are effectively disabled in order to prevent loops
	- these interfaces **still recieve BPDUS**, but do **not forward STP BPDUs**
	- these interfaces do **not learn MAC addresses**
- **forwarding** -  the port will forward traffic. These ports are **root or designated**
These states are **stable** and do not change as long as there are no changes in the network topology
there are two other states you should be aware of:
| STP Port State | Stable/Transitional |
| -------------- | ------------------- |
| Blocking       | Stable              |
| Listening      | Transitional        |
| Learning       | Transitional        |
| Forwarding     | Stable              | 

#### Listening
- After the **blocking state**, ports with a **designated or root role** enter the **listening** state
- **Only Designated or Root ports enter the listening state**
- the listening state is **15 seconds long** by default. this is determined by the **forward delay** timer
- an interface in the listening state **ONLY** forwards/recieves  STP BPDUs
- an interface in the listening state **does not** forward or recieve regular traffic
- An interface in the listening state **does not** learn MAC addresses from regular traffic that arrives on the interface
#### Learning
- after the **listening state**, a designated or root port will enter the **learning** state
- the learning state is **15 seconds long** by default. this is determined by the **forward delay** timer (the same timer is used for listening and learning states)
	- this means that by default it takes a total of **30 seconds** to move through these states
- an interface in the learning state **only sends/recieves BPDUs**
- an interface in the learning state **does not** forward or recieve regular traffic
- an interface in the learning state **DOES LEARN** MAC addresses from regular traffic
	- this means that the interface is **getting ready** to forward traffic
#### Summary
Here is a summary chart of each port state
| STP port state | Send/Recieve BPDU | Frame Forwarding (regular traffic) | MAC address learning | Stable/transitional |
| -------------- | ----------------- | ---------------------------------- | -------------------- | ------------------- |
| Blocking       | NO/YES            | NO                                 | NO                   | Stable              |
| Listening      | YES/YES           | NO                                 | NO                   | Transitional        |
| Learning       | YES/YES           | NO                                 | YES                  | Transitional        |
| Forwarding     | YES/YES           | YES                                | YES                  | Stable              |
| Disabled       | NO/NO             | NO                                 | NO                   | Stable                    |
### Spanning Tree Timers
| STP Timer     | Purpose                                                                                         | Duration           |
| ------------- | ----------------------------------------------------------------------------------------------- | ------------------ |
| Hello         | How often the root bridge sends hellp BPDUs                                                     | 2sec               |
| Forward Delay | How long the switch will stay in the listening and learning states (Each state is 15 sec)       | 15sec              |
| Max Age       | How long an interface will wait **after ceasing to recieve Hello BPDUs** to change STP topology | 20 sec (10xhellos) | 
Above is a chart demonstrating Spanning tree timer default values. 
#### Hello Timer
- When switches first initialize, they assume they are the root bridge and send BPDU's in all directions. However, once the network has converged and stabilized, only the **root bridge** will send BPDUs.
- The other switches will then forward these values, adding information such as root cost, sending bridge ID, and sending port ID. after 2 seconds, the process repeats. 
- These frames are forwarded only out of their **designated ports**, not root or non-designated ports.
#### Forward Delay Timer
- Length of the transitional state of each **listening and learning** state
- this makes the time a total of 30 seconds to transition through these states
- remember each collision domain will have a **designated port**
- remember all **root ports** and **non-designated ports** will expect to recieve BPDU's, and all **designated ports** will send BPDUs
- after every reciept of a BPDU, the port will start to count down from 20. 
- if another BPDU is recieved before the max age timer counts down to 0, the timer will reset and no changes occur
- if another BPDU is not recieved and the timer counts down to 0, the switch will reeavaluate its STP choices. In this case it will transition like this:
	- blocking --> learning(15sec) --> listening(15sec) --> forwarding
- it can take a total of **50 seconds** for a blocking interface to transition to forwarding
- why does it take so long? to make sure that there are no loops that are accidentally created.
- a forwarding interface does not need to transition to a blocking state, it can move instantly.
#### Max Age Timer
- This timer indicates how long an interface should wait after ceasing to recieve BPDUs.
## STP BPDU
Lets look at an STP BPDU
![[Pasted image 20230323141152.png]]
- Notice that the MAC address for PVST+ is **01:00:0c:cc:cc:cd**
	- this is a bit of trivia, but you may need to know it for the test
	- the plus indicates this supports dot1q encapsulation
-  The first three fields are:
	- the protocol identifier (0x0000)
	- the version identifier (0)
	- the BPDU type (0x00)
		- there are other types,  but we don't need to know them
- There are BPDU flags, but you won't need to know them for the exam
- Next is the root identifier. 
	- the bridge priority (32768)
	- the vlan ID (10)
	- the bridge system ID (this would be the MAC address of the root bridge)
- Next is the root path cost (0)
	- because the root path cost is 0, this must be the root bridge
- next is the bridge identifier
	- this is the same as the root identifier, but it has the information of the last switch that forwarded the frame
- next is the port identifier, it tells which port sent this BPDU
	- the first half is the port priority
	- the second half is the port number
- finally is the timers
	- message age: (inverse of max age)
	- max age
	- hello time
	- forward delay
## STP Optional Features (STP Toolkit)
### Portfast
- portfast can be enabled on interfaces connected to end hosts. 
- When these ports are turned on, they must go through listening and learning states before being turned on, which will take 30 seconds.
- portfast allows you to skip this transition step when being connected to end hosts instead of switches
- if enabled on a port connected to another switch it could cause a layer 2 loop
- to enable, select an interface and enter **spanning-tree portfast**
- this can only be enabled on access ports
### BPDU Guard
- if an interface with BPDU guard enabled receives a BPDU from another switch, the interface will be shut down to prevent a loop
- this is to protect against loops
- select an interface and enter **spanning-tree bpduguard enable**
- to re-enable a port that has been shutdown by BPDU guard, use **shutdown** then **no shut**
	- if you didn't actually fix the problem, it will just be shut down again 
### Root Guard
- if you enable root guard on an interface, the switch will not accept any other switch as root, even if it has a lower bridge ID as the current root. 
- if it recieves a highter priority root switch, the interface will be disabled
### Loop Guard
- if you enable this, even if the interface stops receiving BPDUs, it will not start forwarding. This interface will be disabled.

## STP Configurations
- configure spanning tree mode with **spanning-tree mode ?**
	- options are:
	- **mst
	- **pvst**
	- **rapid-pvst**
- to change bridge priority you can use **spanning-tree vlan 1 root primary**
	- this will set the stp priority to 24576
	- if another switch already has this, it will set it to 4096 less. 
	- the vlan number affects what vlan this switch is root on
- to set the secondary root bridge **spanning-tree vlan 1 root secondary**
	- sets the stp priority to 28672
- to manually set priority **spanning-tree vlan 1 priority number**
	- remember that you can only set priority to multiples of 4096
- to set the cost of an interface, select an interface and use **spanning-tree vlan 1 cost 100**
- to set the port priority, use **spanning-tree vlan 1 port-priority num**
### Spanning Tree Load Balancing
In cases where you have multiple VLANS in your network, you can configure different root bridges for different vlans.  This means that SPT can load balance between different VLANS by enabling or disabling different interfaces per VLAN