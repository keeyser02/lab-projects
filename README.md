# lab-projects
# 1 DHCP & NAT Configuration

## Objective
Configure DHCP to assign IP addresses and NAT overload for internet access.

## IP Address Plan
| Interface/Device | IP Address     | Subnet Mask     |
|------------------|---------------|----------------|
| Router0 Gi0/1    | 192.168.1.1   | 255.255.255.0  |
| Router0 Gi0/0    | 200.0.0.2     | 255.255.255.252|
| ISP              | 200.0.0.1     | 255.255.255.252|

## Router Configuration
```
enable
configure terminal
ip dhcp pool LAN
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8
interface gi0/1
 ip address 192.168.1.1 255.255.255.0
 ip nat inside
 no shutdown
interface gi0/0
 ip address 200.0.0.2 255.255.255.252
 ip nat outside
 no shutdown
access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 interface gi0/0 overload
exit
write memory
```

## Testing
- PCs should get IPs automatically.
- Ping the ISP’s public IP.

- # IPv6 Network Setup

## Objective
Configure IPv6 addressing and static routing between two routers.

## IPv6 Address Plan
| Device | Interface | IPv6 Address       | Prefix |
|--------|-----------|-------------------|--------|
| R1     | Gi0/0     | 2001:DB8:1::1     | /64    |
| R1     | Gi0/1     | 2001:DB8:2::1     | /64    |
| R2     | Gi0/1     | 2001:DB8:2::2     | /64    |
| PC     | NIC       | 2001:DB8:1::10    | /64    |

## Router Configuration (R1 example)
```
enable
configure terminal
interface gi0/0
 ipv6 address 2001:DB8:1::1/64
 ipv6 enable
 no shutdown
interface gi0/1
 ipv6 address 2001:DB8:2::1/64
 ipv6 enable
 no shutdown
ipv6 route ::/0 2001:DB8:2::2
exit
write memory
```

## Testing
- Ping from PC to R2 IPv6 address.

  # Network Security with ACLs

## Objective
Implement ACLs to restrict traffic between VLANs.

## Scenario
- Allow VLAN 10 to communicate with VLAN 30.
- Block VLAN 20 from accessing VLAN 30.

## Router Configuration Example
```
enable
configure terminal
access-list 100 deny ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255
access-list 100 permit ip any any
interface gi0/0.20
 ip access-group 100 in
exit
write memory
```

## Testing
- From VLAN 20 to VLAN 30 → should fail.
- From VLAN 10 to VLAN 30 → should succeed.


