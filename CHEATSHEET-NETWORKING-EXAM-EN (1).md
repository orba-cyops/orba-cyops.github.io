# Networking Exam Cheatsheet — 25.12.2025

---

# Project Summary — What Was Implemented

### Router-on-a-Stick
Routing between VLANs using sub-interfaces on a single physical router interface. One physical cable carries all VLAN traffic, separated logically by dot1Q tags.

### VLAN Segmentation (3 VLANs per site)
Logical separation of networks at each site for organization and security. Each site has dedicated VLANs for different user groups (e.g., staff, management, servers).

### ISP Network on R4 (/24)
A dedicated network simulating an Internet Service Provider with a central gateway that all internal traffic exits through.

### Dynamic Routing — OSPF
Dynamic routing between routers to achieve full and stable connectivity. OSPF automatically discovers and shares routes between all routers.

### NAT (PAT Overload on R4)
Address translation that allows devices on internal networks to reach external networks using a single public IP address (Port Address Translation).

### DHCP with Helper-Address
A central DHCP server with relay agents configured per VLAN, so devices receive IP addresses automatically even when they are on a different subnet from the DHCP server.

### STP Root per VLAN
A designated root switch is elected per VLAN to prevent Layer 2 loops (broadcast storms) in the switch fabric.

### Port Security (Sticky + Limit 1)
Edge port hardening by restricting the number of allowed MAC addresses and locking them dynamically via Sticky MAC.

### Static Routes Support
Supplementary static routes ensure connectivity and communication continuity alongside OSPF.

### ARP & Connectivity Testing
Layer 2 and Layer 3 validation tests to confirm the network is functioning as designed.

### Consistent Network Topology
A uniform network structure including Trunk links between switches and WAN links between sites.

---

# Project Presentation Script (Professional & Clear)

In this project, I designed and built a distributed network infrastructure spanning multiple sites (R1–R3), connected via WAN links. Each site has three separate VLANs for logical segmentation between user types and services.

On each router, I implemented the **Router-on-a-Stick** model — inter-VLAN routing is handled via sub-interfaces on a single physical interface, using a dot1Q Trunk toward the switches.

For IP address management, I designed a **detailed IP plan** with precise subnetting, management networks, and WAN links in /30 format.

To serve IP addresses to end devices, I deployed a **central DHCP server** and configured **DHCP Relay (ip helper-address)** on each VLAN — allowing devices to receive addresses automatically even when the server is on a different subnet. Default routes and supplementary static routes were also configured alongside **OSPF**, providing dynamic routing and full connectivity across all sites.

For internet egress, I implemented **NAT PAT Overload** on R4, allowing all internal devices to exit through a single public IP. At Layer 2, I applied **Spanning Tree Root-Per-VLAN** to prevent loops, and **Port-Security** to restrict the number of devices per port and enforce Sticky MAC at edge ports.

Finally, I ran a full series of validation tests — **ARP, Ping, DHCP Bindings, OSPF Neighbors, and NAT Translations** — to confirm all network components operate stably and consistently according to the designed topology.

---

## One-Sentence Summary (for presentations)

> "In this project I built a complete enterprise network with segmentation, dynamic routing, NAT, centralized DHCP, and security hardening — following a Top-Down design approach and ensuring end-to-end connectivity and stability."

---

# 1) Full IP & VLAN Plan

> **Legend:**
> - `sbnm` = subnet mask (last octet in decimal)
> - `netid` = network ID (last octet)
> - `f id` = first usable host
> - `dg` = default gateway (last usable host, assigned to router sub-interface)
> - `bc` = broadcast address

---

## R1 — Site 1

```
VLAN 10 (/27)
Range: 192.168.1.0 – 192.168.1.31
Mask: 255.255.255.224 (sbnm 224)
Network: 192.168.1.0
First: 192.168.1.1
Gateway: 192.168.1.30 assigned to g8/0.10
Bcast: 192.168.1.31
```

```
VLAN 20 (/27)
Range: 192.168.1.32 – 192.168.1.63
Mask: 255.255.255.224 (sbnm 224)
Network: 192.168.1.32
First: 192.168.1.33
Gateway: 192.168.1.62 assigned to g8/0.20
Bcast: 192.168.1.63
```

```
VLAN 30 (/28)
Range: 192.168.1.64 – 192.168.1.79
Mask: 255.255.255.240 (sbnm 240)
Network: 192.168.1.64
First: 192.168.1.65
Gateway: 192.168.1.78 assigned to g8/0.30
Bcast: 192.168.1.79
```

---

## R2 — Site 2

```
VLAN 40 (/25)
Range: 192.168.2.0 – 192.168.2.127
Mask: 255.255.255.128 (sbnm 128)
Network: 192.168.2.0
First: 192.168.2.1
Gateway: 192.168.2.126 assigned to g4/0.40
Bcast: 192.168.2.127
```

```
VLAN 50 (/27)
Range: 192.168.2.128 – 192.168.2.159
Mask: 255.255.255.224 (sbnm 224)
Network: 192.168.2.128
First: 192.168.2.129
Gateway: 192.168.2.158 assigned to g4/0.50
Bcast: 192.168.2.159
```

```
VLAN 60 (/28)
Range: 192.168.2.160 – 192.168.2.175
Mask: 255.255.255.240 (sbnm 240)
Network: 192.168.2.160
First: 192.168.2.161
Gateway: 192.168.2.174 assigned to g4/0.60
Bcast: 192.168.2.175
```

---

## R3 — Site 3

```
VLAN 70 (/27)
Range: 192.168.3.0 – 192.168.3.31
Mask: 255.255.255.224 (sbnm 224)
Network: 192.168.3.0
First: 192.168.3.1
Gateway: 192.168.3.30 assigned to g9/0.70
Bcast: 192.168.3.31
```

```
VLAN 80 (/28)
Range: 192.168.3.32 – 192.168.3.47
Mask: 255.255.255.240 (sbnm 240)
Network: 192.168.3.32
First: 192.168.3.33
Gateway: 192.168.3.46 assigned to g9/0.80
Bcast: 192.168.3.47
```

```
VLAN 90 (/29)
Range: 192.168.3.48 – 192.168.3.55
Mask: 255.255.255.248 (sbnm 248)
Network: 192.168.3.48
First: 192.168.3.49
Gateway: 192.168.3.54 assigned to g9/0.90
Bcast: 192.168.3.55
```

---

## R4 — ISP (simulated Internet)

```
/24
Range: 192.168.10.0 – 192.168.10.255
Mask: 255.255.255.0
Network: 192.168.10.0
First: 192.168.10.1
Gateway: 192.168.10.254
Bcast: 192.168.10.255
```

## R5 — DHCP Server

```
/24
Range: 192.168.100.0 – 192.168.100.255
Mask: 255.255.255.0
Network: 192.168.100.0
First: 192.168.100.1
Gateway: 192.168.100.254
Bcast: 192.168.100.255
```

## R6 — WiFi

```
/24
Range: 192.168.200.0 – 192.168.200.255
Mask: 255.255.255.0
Network: 192.168.200.0
First: 192.168.200.1
Gateway: 192.168.200.254
Bcast: 192.168.200.255
```

---

## WAN Links Between Routers (/30)

> Each /30 gives exactly 2 usable IPs — perfect for point-to-point router links.

| Link | Network | Mask | Router A IP | Router B IP |
|-------|------------|------|--------------|--------------|
| R1–R2 | 10.0.0.0 | /30 | 10.0.0.1 (R1 g9/0) | 10.0.0.2 (R2 g9/0) |
| R2–R3 | 10.0.0.4 | /30 | 10.0.0.5 (R2 g8/0) | 10.0.0.6 (R3 g8/0) |
| R3–R1 | 10.0.0.8 | /30 | 10.0.0.9 (R3 g7/0) | 10.0.0.10 (R1 g7/0) |
| R3–R4 | 10.0.0.12 | /30 | 10.0.0.13 (R3 g6/0) | 10.0.0.14 (R4 g6/0) |
| R3–R5 | 10.0.0.16 | /30 | 10.0.0.17 (R3 g5/0) | 10.0.0.18 (R5 g5/0) |
| R3–R6 | 10.0.0.20 | /30 | 10.0.0.21 (R3 g4/0) | 10.0.0.22 (R6 g4/0) |

---

# 2) Router Configurations

---

## R1 (Full Config)

> **What this does:** Configures sub-interfaces for VLANs 10/20/30, WAN links to R2 and R3, enables OSPF, and adds static default/backup routes.

```
conf t
hostname R1
interface g8/0
no shut

! --- Sub-interfaces for VLAN routing (Router-on-a-Stick) ---
! Each sub-interface handles one VLAN's traffic via dot1Q tag.
! The ip helper-address forwards DHCP requests to the central server.

interface g8/0.10
 encapsulation dot1q 10
 ip address 192.168.1.30 255.255.255.224
 ip helper-address 192.168.100.1 ! Forward DHCP requests to R5

interface g8/0.20
 encapsulation dot1q 20
 ip address 192.168.1.62 255.255.255.224
 ip helper-address 192.168.100.1

interface g8/0.30
 encapsulation dot1q 30
 ip address 192.168.1.78 255.255.255.240
 ip helper-address 192.168.100.1

! --- WAN interfaces ---
interface g9/0
 ip address 10.0.0.1 255.255.255.252 ! Link to R2
 no shut

interface g7/0
 ip address 10.0.0.10 255.255.255.252 ! Link to R3
 no shut
ex

! --- OSPF: advertise local networks to all neighbors ---
router ospf 1
 router-id 1.1.1.1
 network 192.168.1.0 0.0.0.255 area 0 ! Advertise VLAN subnets
 network 10.0.0.0 0.0.0.3 area 0 ! Advertise R1-R2 WAN link
 network 10.0.0.8 0.0.0.3 area 0 ! Advertise R1-R3 WAN link
ex

! --- Static routes (backup/supplementary) ---
en
conf t
ip route 0.0.0.0 0.0.0.0 10.0.0.2 ! Default route via R2
ip route 0.0.0.0 0.0.0.0 10.0.0.9 ! Default route via R3
ip route 198.168.2.0 255.255.255.0 10.0.0.3
ip route 198.168.3.0 255.255.255.0 10.0.0.9
```

```
! DHCP - Non-Server Option (if DHCP is configured locally on R1 instead of R5)

ip dhcp excluded-address 192.168.1.1 192.168.1.20 ! Reserve IPs for static devices
ip dhcp excluded-address 192.168.1.126 192.168.1.126
ip dhcp excluded-address 192.168.1.190 192.168.1.190
ip dhcp excluded-address 192.168.1.206 192.168.1.206

ip dhcp pool VLAN10
 network 192.168.1.0 255.255.255.128
 default-router 192.168.1.126

ip dhcp pool VLAN20
 network 192.168.1.128 255.255.255.192
 default-router 192.168.1.190

ip dhcp pool VLAN30
 network 192.168.1.192 255.255.255.240
 default-router 192.168.1.206

end
wr
```

---

## R2 (Full Config)

> **What this does:** Configures sub-interfaces for VLANs 40/50/60, WAN links to R1 and R3, enables OSPF, and adds static routes.

```
conf t
hostname R2

interface g4/0
 no shut

interface g4/0.40
 encapsulation dot1q 40
 ip address 192.168.2.126 255.255.255.128
 ip helper-address 192.168.100.1

interface g4/0.50
 encapsulation dot1q 50
 ip address 192.168.2.158 255.255.255.224
 ip helper-address 192.168.100.1

interface g4/0.60
 encapsulation dot1q 60
 ip address 192.168.2.174 255.255.255.240
 ip helper-address 192.168.100.1

interface g8/0
 ip address 10.0.0.5 255.255.255.252 ! Link to R3
 no shut

interface g9/0
 ip address 10.0.0.2 255.255.255.252 ! Link to R1
 no shut
ex

router ospf 1
 router-id 2.2.2.2
 network 192.168.2.0 0.0.0.255 area 0
 network 10.0.0.0 0.0.0.3 area 0
 network 10.0.0.4 0.0.0.3 area 0
ex

en
conf t
ip route 198.168.1.0 255.255.255.0 10.0.0.0
ip route 198.168.3.0 255.255.255.0 10.0.0.7
ip route 0.0.0.0 0.0.0.0 10.0.0.6
ip route 0.0.0.0 0.0.0.0 10.0.0.1
```

```
! DHCP - Non-Server Option

ip dhcp excluded-address 192.168.2.1 192.168.2.20
ip dhcp excluded-address 192.168.2.126 192.168.2.126
ip dhcp excluded-address 192.168.2.190 192.168.2.190
ip dhcp excluded-address 192.168.2.206 192.168.2.206

ip dhcp pool VLAN40
 network 192.168.2.0 255.255.255.128
 default-router 192.168.2.126

ip dhcp pool VLAN50
 network 192.168.2.128 255.255.255.192
 default-router 192.168.2.190

ip dhcp pool VLAN60
 network 192.168.2.192 255.255.255.240
 default-router 192.168.2.206

end
wr
```

---

## R3 (Full Config)

> **What this does:** R3 is the hub router — it connects to R1, R2, R4 (ISP), R5 (DHCP Server), and R6 (WiFi). All inter-site traffic passes through R3.

```
conf t
hostname R3
interface g9/0
 no shut

interface g9/0.70
 encapsulation dot1q 70
 ip address 192.168.3.30 255.255.255.224
 ip helper-address 192.168.100.1

interface g9/0.80
 encapsulation dot1q 80
 ip address 192.168.3.46 255.255.255.240
 ip helper-address 192.168.100.1

interface g9/0.90
 encapsulation dot1q 90
 ip address 192.168.3.54 255.255.255.248
 ip helper-address 192.168.100.1

interface g7/0
 ip address 10.0.0.9 255.255.255.252 ! Link to R1
 no shut

interface g8/0
 ip address 10.0.0.6 255.255.255.252 ! Link to R2
 no shut

interface g6/0
 ip address 10.0.0.13 255.255.255.252 ! Link to R4 (ISP)
 no shut

interface g5/0
 ip address 10.0.0.17 255.255.255.252 ! Link to R5 (DHCP Server)
 no shut

interface g4/0
 ip address 10.0.0.21 255.255.255.252 ! Link to R6 (WiFi)
 no shut
ex

router ospf 1
 router-id 3.3.3.3
 network 192.168.3.0 0.0.0.255 area 0
 network 10.0.0.8 0.0.0.3 area 0
 network 10.0.0.12 0.0.0.3 area 0
 network 10.0.0.4 0.0.0.3 area 0
 network 10.0.0.20 0.0.0.3 area 0
ex

en
conf t
ip route 0.0.0.0 0.0.0.0 10.0.0.18
ip route 0.0.0.0 0.0.0.0 10.0.0.22
ip route 0.0.0.0 0.0.0.0 10.0.0.10
ip route 0.0.0.0 0.0.0.0 10.0.0.5
ip route 198.168.10.0 255.255.255.0 10.0.0.14
ip route 198.168.100.0 255.255.255.0 10.0.0.18
ip route 198.168.1.0 255.255.255.0 10.0.0.10
ip route 198.168.2.0 255.255.255.0 10.0.0.5
```

```
! DHCP - Non-Server Option (R3)

ip dhcp excluded-address 192.168.3.1 192.168.3.20
ip dhcp excluded-address 192.168.3.126 192.168.3.126
ip dhcp excluded-address 192.168.3.190 192.168.3.190
ip dhcp excluded-address 192.168.3.206 192.168.3.206

ip dhcp pool VLAN70
 network 192.168.3.0 255.255.255.128
 default-router 192.168.3.126

ip dhcp pool VLAN80
 network 192.168.3.128 255.255.255.192
 default-router 192.168.3.190

ip dhcp pool VLAN90
 network 192.168.3.192 255.255.255.240
 default-router 192.168.3.206

end
wr
```

---

## R4 — ISP Router

> **What this does:** Simulates the ISP. It receives all internal traffic via NAT and routes it to the outside world. PAT Overload allows many internal IPs to share one public IP.
>
> **Example flow:** PC on VLAN 10 (192.168.1.5) wants to reach the internet R4 translates it to 192.168.10.254 traffic exits via g4/0 (NAT outside).

```
conf t
hostname R4

! g6/0 = toward R3 (internal side)
! g4/0 = toward "Internet" (outside)

interface g6/0
 ip address 10.0.0.14 255.255.255.252
 no shut
 ip nat outside ! Traffic leaving toward internet

interface g4/0
 ip address 192.168.10.254 255.255.255.0
 no shut
 ip nat inside ! Traffic coming from internal networks
ex

ip route 0.0.0.0 0.0.0.0 10.0.0.13 ! Default route back to R3

router ospf 1
 router-id 10.10.10.10
 network 10.0.0.14 0.0.0.3 area 0
 network 192.168.10.0 0.0.0.255 area 0
ex

! NAT ACL: define which internal networks are allowed out
access-list 1 permit 192.168.1.0 0.0.0.255
access-list 1 permit 192.168.2.0 0.0.0.255
access-list 1 permit 192.168.3.0 0.0.0.255

! PAT Overload: translate all ACL-matched traffic using g4/0's IP
ip nat inside source list 1 interface gigabitEthernet4/0 overload

end
wr
```

---

## R5 — DHCP Server Router

> **What this does:** Acts as the central DHCP server for the entire network. All routers forward DHCP requests here via `ip helper-address`.
>
> **Example:** PC on VLAN 10 sends a DHCP broadcast R1 relays it to 192.168.100.1 (R5) R5 responds with an IP from the correct pool.

```
conf t
hostname R-SERVER

int g5/0
 ip address 10.0.0.18 255.255.255.252 ! WAN link toward R3
 no sh

int g4/0
 ip address 192.168.100.254 255.255.255.0 ! DHCP server address
 no sh

ip route 0.0.0.0 0.0.0.0 10.0.0.17 ! Default route to R3

router ospf 1
 router-id 100.100.100.100
 network 10.0.0.16 0.0.0.3 area 0
 network 192.168.100.0 0.0.0.255 area 0
ex
```

---

## R6 — WiFi Router

> **What this does:** Handles the WiFi segment (192.168.200.0/24). Sub-interface g5/0.200 relays DHCP requests to R5 so wireless clients get IPs automatically.

```
conf t
hostname R-WIFI

int g4/0
 ip address 10.0.0.22 255.255.255.252 ! WAN link toward R3
 no sh

int g5/0
 ip address 192.168.200.254 255.255.255.0
 no sh

int g5/0.200
 ip helper-address 192.168.100.1 ! Forward WiFi DHCP requests to R5
ex

ip route 0.0.0.0 0.0.0.0 10.0.0.21 ! Default route to R3

router ospf 1
 router-id 200.200.200.200
 network 10.0.0.20 0.0.0.3 area 0
 network 192.168.100.0 0.0.0.255 area 0
ex
```

---

# 3) Switch Configurations

> **Design rule:** Each switch stack has 3 switches, one per VLAN. The switch that "owns" a VLAN becomes its STP root. Trunk ports carry all 3 VLANs between switches. Access ports connect end devices.

---

## SW-1 (Site 1 — VLANs 10, 20, 30)

### SW-1-1 (STP Root for VLAN 10)
```
en
conf t
hostname SW-1-1

! Trunk ports carry traffic for all 3 VLANs
interface range g9/1, g7/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30

interface range g8/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30

! Access port: connects end device to VLAN 10
interface fa5/1
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast ! Skip STP delay for end devices
ex

! Elect this switch as STP root for VLAN 10
spanning-tree vlan 10 root primary

! Create VLAN database entries
vlan 20
ex
vlan 30
ex

end
wr
```

```
! PORT SECURITY (optional — harden edge ports)
! Allows only 1 MAC per port; if violated port shuts down
en
conf t

int f5/1
 switchport port-security
 switchport port-security maximum 1 ! Max 1 device
 switchport port-security mac-address sticky ! Learn and lock MAC automatically
 switchport port-security violation shutdown ! Shut port if violated
end
wr
```

### SW-1-2 (STP Root for VLAN 20)
```
en
conf t
hostname SW-1-2

interface range g8/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30

interface fa5/1
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast

spanning-tree vlan 20 root primary

vlan 10
ex
vlan 30
ex

end
wr
```

```
! PORT SECURITY (optional)
en
conf t

int f5/1
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
end
wr
```

### SW-1-3 (STP Root for VLAN 30)
```
en
conf t
hostname SW-1-3

interface range g8/1, g9/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30

interface fa5/1
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
ex

spanning-tree vlan 30 root primary

vlan 10
ex
vlan 20
ex

end
wr
```

```
! PORT SECURITY (optional)
en
conf t

int f5/1
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
end
wr
```

---

## SW-2 (Site 2 — VLANs 40, 50, 60)

### SW-2-1 (STP Root for VLAN 40)
```
en
conf t
hostname SW-2-1

interface range g8/1, g7/1
 switchport mode trunk
 switchport trunk allowed vlan 40,50,60

interface range g6/1
 switchport mode trunk
 switchport trunk allowed vlan 40,50,60

interface fa5/1
 switchport mode access
 switchport access vlan 40
 spanning-tree portfast
ex

spanning-tree vlan 40 root primary

vlan 50
ex
vlan 60
ex

end
wr
```

```
! PORT SECURITY (optional)
en
conf t

int f5/1
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
end
wr
```

### SW-2-2 (STP Root for VLAN 50)
```
en
conf t
hostname SW-2-2

interface range g6/1
 switchport mode trunk
 switchport trunk allowed vlan 40,50,60

interface fa5/1
 switchport mode access
 switchport access vlan 50
 spanning-tree portfast

spanning-tree vlan 50 root primary

vlan 40
ex
vlan 60
ex

end
wr
```

```
! PORT SECURITY (optional)
en
conf t

int f5/1
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
end
wr
```

### SW-2-3 (STP Root for VLAN 60)
```
en
conf t
hostname SW-2-3

interface range g7/1, g6/1
 switchport mode trunk
 switchport trunk allowed vlan 40,50,60

interface fa5/1
 switchport mode access
 switchport access vlan 60
 spanning-tree portfast
ex

spanning-tree vlan 60 root primary

vlan 40
ex
vlan 50
ex

end
wr
```

```
! PORT SECURITY (optional)
en
conf t

int f5/1
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
end
wr
```

---

## SW-3 (Site 3 — VLANs 70, 80, 90)

### SW-3-1 (STP Root for VLAN 70)
```
en
conf t
hostname SW-3-1

interface range g8/1, g6/1
 switchport mode trunk
 switchport trunk allowed vlan 70,80,90

interface range g9/1
 switchport mode trunk
 switchport trunk allowed vlan 70,80,90

interface fa5/1
 switchport mode access
 switchport access vlan 70
 spanning-tree portfast
ex

spanning-tree vlan 70 root primary

vlan 80
ex
vlan 90
ex

end
wr
```

```
! PORT SECURITY (optional)
en
conf t

int f5/1
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
end
wr
```

### SW-3-2 (STP Root for VLAN 80)
```
en
conf t
hostname SW-3-2

interface range g7/1
 switchport mode trunk
 switchport trunk allowed vlan 70,80,90

interface fa5/1
 switchport mode access
 switchport access vlan 80
 spanning-tree portfast
ex

spanning-tree vlan 80 root primary

vlan 70
ex
vlan 90
ex

end
wr
```

```
! PORT SECURITY (optional)
en
conf t

int f5/1
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
end
wr
```

### SW-3-3 (STP Root for VLAN 90)
```
en
conf t
hostname SW-3-3

interface range g7/1, g6/1
 switchport mode trunk
 switchport trunk allowed vlan 70,80,90

interface fa5/1
 switchport mode access
 switchport access vlan 90
 spanning-tree portfast

spanning-tree vlan 90 root primary

vlan 70
ex
vlan 80
ex

end
wr
```

```
! PORT SECURITY (optional)
en
conf t

int f5/1
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
end
wr
```

---

# Quick Verification Commands

> Run these on any router or switch to confirm the config is working.

```bash
show ip int brief # Check interface IPs and status (Up/Down)
show ip route # View the full routing table (static + OSPF)
show ip ospf neighbor # Verify OSPF neighbors are established
show ip dhcp binding # Check which IPs were assigned by DHCP
show ip nat translations # Verify NAT/PAT is translating correctly
show spanning-tree # Check STP root per VLAN
show port-security # Verify sticky MACs and violation counters
```

---

# ARP & Connectivity Tests

> Use these to validate Layer 2 and Layer 3 reachability.

```bash
show ip arp # View the ARP table (IP-to-MAC mappings)
ping <gateway-ip> # Ping the default gateway to test Layer 3 reachability
ping <other-router-ip> # Ping across WAN links to verify routing
ping <dns-server-ip> # Ping DNS from a VLAN to test inter-VLAN routing + relay
```

**Example ping sequence from a PC in VLAN 10:**
1. `ping 192.168.1.30` test gateway reachability
2. `ping 10.0.0.2` test WAN link to R2
3. `ping 192.168.100.1` test DHCP server reachability
4. `ping 192.168.10.254` test path to ISP (R4)

---

# What Was Delivered — Summary Checklist

 **Router-on-a-Stick** — Inter-VLAN routing via sub-interfaces
 **Precise IP Design** — Subnets, gateways, and /30 WAN links
 **DHCP Relay + Pools** — Helper-address forwarding and per-VLAN pools
 **OSPF Multi-Area Core** — Dynamic route propagation between routers
 **Supplementary Static Routes** — Default routes and edge connectivity
 **NAT on R4 (ISP)** — PAT Overload for WAN access
 **STP Root Per-VLAN** — Stable topology, no Layer 2 loops
 **Port-Security** — Device limiting + Sticky MAC protection at edges
 **Consistent Trunk Topology** — Trunks between switches for all VLAN groups
 **Isolated segments** — Dedicated networks for Server, WiFi, and ISP

---

```
! Router Banner (Humor — optional)
conf t
banner motd #
************************************************************
* Welcome to the Lab — Also known as: *
* "Forget it… Tracer!" Edition *
* *
* Special thanks to Moshe — the mentor who taught us *
* that when the topology breaks… we don't panic — *
* we just say: *
* >> forget it tracer, let's troubleshoot *
* *
* Built with: VLANs, OSPF, coffee injections, *
* and at least 3 moments of "why is this not working?" *
* *
* Security mindset? Persistence? *
* Turning chaos into configs? *
* *
* Dedicated to the teacher who made learning fun, *
* breaking networks educational — and fixing them heroic. *
* *
* — Student Project | Humor - Learning - Resilience *
************************************************************
#
end
wr
```

```
! Switch Banner (Humor — optional)
conf t
banner motd #
************************************************************
* WELCOME TO THE CHAOS SWITCH - LAYER 2 MADNESS *
* This is not a network. *
* This is a battlefield of VLANs, MAC tables and STP. *
* RULE 1: *
* If something breaks — *
* don't panic. *
* Just say: *
* "Forget it... Tracer!" *
* Broadcast storms may cry. *
* Trunks may collapse. *
* Port-security will still bully someone. *
* Here we debug, we suffer, we learn, we conquer. *
* *
* — SWITCH LAB CREW | Chaos - Coffee - Config *
************************************************************
#
end
wr
```
