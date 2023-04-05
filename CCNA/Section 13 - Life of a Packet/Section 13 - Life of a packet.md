## Network Topology
![[Pasted image 20230311152646.png]]
Assume we have preconfigured static routes on this device as:
PC1-->R1-->R2-->R4-->PC4 and vice versa
we will also be using MAC addresses for this lecure. The MAC addresses will be simplified to only 4 bytes
| Device   | MAC  |
| -------- | ---- |
| PC1      | 1111 |
| R1 Gi0/2 | aaaa |
| R1 Gi0/0 | bbbb |
| R2 Gi0/0 | cccc |
| R2 Gi0/1 | dddd |
| R4 Gi0/1 | eeee |
| R4 Gi0/2 | fffe |
| PC4      | 4444 | 
Note that R4 Gi0/2 does not have a MAC of ffff because that would be a **broadcast** MAC.
the src will be 192.168.1.1
the dst will be 192.1668.4.1
## Sending a packet to a remote destination
- the src will be 192.168.1.1
- the dst will be 192.1668.4.1
Because this is an address that is **not in the LAN**, PC1 knows it needs to forward its information to the **default gateway**. This is the first packet PC1 has sent, so it must use ARP

### ARP, Encapsulation, and de-encapsulation
- PC1 sends an ARP request as follows:
```
ARP Request
Src IP: 192.168.1.1
Dst IP: 192.168.1.254
Dst MAC: ffff
Src MAC: 1111
```
- the destination MAC is broadcast, and the source MAC is it's own MAC address
- it sends the frame, and SW1 floods the frame out of all interfaces except the one it came in on.
- R1 recieves the frame and responds with an ARP response, because the Dst IP is it's own IP. In this process, it learns PC1's IP and MAC:
```
ARP Reply
Src IP: 192.168.1.254
Dst IP: 192.168.1.1
Dst MAC: 1111
Src MAC: aaaa
```
- Now PC1 knows the MAC address of the gateway, so it encapsulates the packet with the Layer 2 header. Now it looks like this:
```
ICMP ECHO Request
Src IP: 192.168.1.1
Dst IP: 192.168.4.1
Dst MAC: aaaa
Src MAC: 1111
```
- R1 recieves this and removes the Layer 2 header and trailer.
- It looks up the dst in its routing table. It sees the next hop as 192.168.12.2
- it needs to encapsulate the packet again with the MAC address of R2, but it doesn't know it yet, so it needs to use ARP
- It sends an ARP request to 192.168.12.2 that looks like this:
```
ARP Request
Src IP: 192.168.12.1
Dst IP: 192.168.12.2
Dst MAC: ffff
Src MAC: bbbb
```
- R2 receives this, and responds with it's own MAC address, because the dst IP matches its own. It learns the MAC and IP from the request
```
ARP Reply
Src IP: 192.168.12.2
Dst IP: 192.168.12.1
Dst MAC: bbbb
Src MAC: cccc
```
- R1 encapsulates the Packet with an ethernet header for R2's MAC
```
ICMP ECHO Request
Src IP: 192.168.1.1
Dst IP: 192.168.4.1
Dst MAC: cccc
Src MAC: bbbb
```
- R2 removes the ethernet header and checks its routing table. It sees next hop as 192.168.24.4
- It sends an ARP request
```
ARP Request
Src IP: 192.168.24.2
Dst IP: 192.168.24.4
Dst MAC: ffff
Src MAC: dddd
```
- R4 Recieves the ARP request and replies, because the dst ip matches its own. It learns the MAC and IP from the request
```
ARP Reply
Src IP: 192.168.24.4
Dst IP: 192.168.24.2
Dst MAC: dddd
Src MAC: eeee
```
- R2 recieves this and re encapsulates the packet
```
ICMP ECHO Request
Src IP: 192.168.1.1
Dst IP: 192.168.4.1
Dst MAC: eeee
Src MAC: cccc
```
- R4 de-encapsulates the packet. It checks its routing table and finds it's connected address. It sends an ARP request.
```
ARP Request
Src IP: 192.168.4.254
Dst IP: 192.168.4.1
Dst MAC: ffff
Src MAC: fffe
```
- PC4 receives the frame and replies, because the dst ip matches its own. It learns the MAC and IP from the request
```
ARP Reply
Src IP: 192.168.4.1
Dst IP: 192.168.4.254
Dst MAC: fffe
Src MAC: 4444
```
- R4 re encapsulates the frame and sends it to PC1. Finally the packet has reached its destination.
```
ICMP ECHO Request
Src IP: 192.168.1.1
Dst IP: 192.168.4.1
Dst MAC: 4444
Src MAC: fffe
```
Notice that the IP src and dst haven't changed, only the MAC L2 headers have changed throughout this process. Also notice the switches never modified the frame at any point.
Now lets say R4 would like to reply. In this case, all devices have went through the ARP process, so the packet can be smoothly forwarded from device to device.