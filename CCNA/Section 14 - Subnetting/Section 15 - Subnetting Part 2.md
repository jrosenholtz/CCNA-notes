## Class C table
| Prefix Length | Number of subnets | Number of hosts |
| ------------- | ----------------- | --------------- |
| /25           | 2                 | 126             |
| /26           | 4                 | 62              |
| /27           | 8                 | 30              |
| /28           | 16                | 14              |
| /29           | 32                | 6               |
| /30           | 64                | 2               |
| /31           | 128               | 0(2)            |
| /32           | 256               | 0(1)                |

## Subnetting Class B networks
There are many more host bits, and therefore many more subnets that can be created with a class B network, but the process is exactly the same. 
Lets look at a problem:
you have been give the 172.16.0.0/16 network. You are asked to create 80 subnets for your companys various LANs. What prefix length should you use?
This seems complicated at first, but you can use the $2^x$ formula to find it. 
borrowing 1 bit gives you 2 networks
2 bits gives you 4 networks
3 bits gives you 8 networks, and so on. The number of bits we need is equivelant to $2^7$ = 128 possible networks, which leaves us with a CIDR notation of /23

