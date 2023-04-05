These topics have been removed from the CCNA, but it still good to know about them
## DTP (Dynamic Trunking Protocol) 
- Cisco proprietary protocol that allows switches to negotiate the type of switchport (**access** or **trunk**) without manual configuration
- enabled by default on all cisco switch interfaces
- so far we have been manually configuring switchports, but with DTP it is not necessary
- For security, manual configuration is recommended. DTP should be disabled on all switchports by default
	- in the **switchport mode** there is a mode we haven't talked about: **dynamic**
	- there are two sub modes to this **auto** and **desirable**
	- a swutchport in **dynamic desirable** will actively try to form a trunk with other cisco switches. it will form a trunk if connected to another switchport in the following modes:
		- **switchport mode trunk**
		- **switchport mode dynamic desirable**
		- **switchport mode dynamic auto**
	- a switchport in **dynamic auto** mode will NOT actively try to form a trunk with other cisco switches, it will only form a trunk if the switch connected to it is actively trying to form a trunk. It will form a trunk with a switchport in the following modes:
		- **switchport mode trunk**
		- **switchport mode dynamic desirable**
- **DTP will not form a trunk with a PC, router, etc. Only access mode.**
- on older switches, **switchport mode dynamic desirable** is the default
- on newer switches, **switchport mode dynamic auto** is the default mode
- you can disable DTP negotiation on an interface with this command: **switchport nonegotiate**
- configuring a port with **switchport mode access** also disables DTP
- switches that support both **802.1Q** and **ISL** trunk encapsulations can use DTP to negotiate the encapsulation they will use
- this negotiation is enabled by default, as the default trunk encapsulation mode is: **switchport trunk encapsulation negotiate**
- **isl** is favored over **802.1Q**, so if both are enabled, ISL will be selected
- DTP frames are sent in VLAN1 when using **ISL**, or in native vlan when using **802.1Q** (The default native VLAN is VLAN1, however)

## VTP (VLAN Trunking Protocol)
- VTP allows you to configure VLANS on a central VTP server switch, and other switches (VTP clients) will synchronize their VLAN database to the server
- it is designed for large networks with many VLANS, so that you don't have to configre each VLAN on every switch
- it is rarely used, and it's recommended you don't use it
- there are three versions: 1, 2, and 3
	- most modern switches support all 3
- there are three modes: **server**,  **client** and **transparent**
- cisco switches operate in **server** mode by default
### VTP servers
- can add, modify, and delete VLANS
- store the database in NVRAM (Non volatile)
- will increase the **revision number** every time a vlan is add/modified/deleted
- VTP servers will advertise the latest version of the db on trunk interfaces, and vtp servers will synchronize their vlan db to it
- **VTP servers are also VTP clients**
- **therefore, a VTP server will sync to another VTP server with a higher revision number**
### VTP clients
- cannot modify VLANS
- do not store VLAN db in NVRAM (**in vtp3 they do**)
- Will sync their VLAN db to the server with the highest revision number in their VTP domain
- will adverties their VLAN db and forward advertisements to other clients over trunk ports
- use **vtp show status** to see the vtp info
- only vtp v3 supports extended VLAN ranges