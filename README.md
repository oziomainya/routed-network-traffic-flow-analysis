# Routed Network Traffic Flow Analysis

> **Category:** Networking
> **Status:** Live
> **Course:** Cisco Networking Basics · Junior Cybersecurity Analyst Career Path
> **Author:** Ozioma Inya         <!-- ✏️ Replace with your name -->
> **Date:** July, 2025              <!-- ✏️ Replace with completion date -->

---

## 📌 Overview

A two-LAN simulation built in Cisco Packet Tracer to observe how traffic behaves
in an unrouted network versus a routed one. Demonstrates IPv4 addressing, default
gateway configuration, and inter-LAN routing using a Cisco router.

---

## 🎯 Objectives

- [x] Build a two-LAN topology with two switches, one router, and four end devices
- [x] Assign static IPv4 addresses and subnet masks to all hosts and router interfaces
- [x] Observe and document why traffic cannot flow between LANs without a configured router
- [x] Configure router interfaces and default gateways to enable inter-LAN routing
- [x] Verify end-to-end connectivity using ping and Packet Tracer simulation mode
- [x] Document all configurations, results, and lessons learned

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| Cisco Packet Tracer | Network topology simulation and traffic visualisation |
| Cisco IOS CLI | Router interface configuration |
| Packet Tracer Simulation Mode | Step-by-step packet flow observation |
| ICMP Ping | End-to-end connectivity verification |

---

## 🧠 Skills Demonstrated

`IPv4 Addressing` `Subnetting` `Default Gateway` `Static IP Configuration`
`Router Interface Config` `Inter-LAN Routing` `ICMP / Ping`
`Cisco IOS CLI` `Network Troubleshooting` `Packet Tracer`

---

## 🗺️ Network Topology

```
  LAN 1 — 192.168.1.0/24                        LAN 2 — 192.168.2.0/24

  [PC1: 192.168.1.10] ──┐                                          ┌── [PC3: 192.168.2.10]
                          ├──[SW1]──[G0/0]──[R1]──[G0/1]──[SW2]──┤
  [PC2: 192.168.1.20] ──┘      192.168.1.1   192.168.2.1           └── [PC4: 192.168.2.20]
```

### Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| PC1 | NIC | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| PC2 | NIC | 192.168.1.20 | 255.255.255.0 | 192.168.1.1 |
| PC3 | NIC | 192.168.2.10 | 255.255.255.0 | 192.168.2.1 |
| PC4 | NIC | 192.168.2.20 | 255.255.255.0 | 192.168.2.1 |
| R1 | Fa0/0 | 192.168.1.1 | 255.255.255.0 | — |
| R1 | Fa0/1 | 192.168.2.1 | 255.255.255.0 | — |
| SW1 | — | — | — | — |
| SW2 | — | — | — | — |

---

## 🔬 Methodology

### Step 1 — Build the Topology
Placed two 2960 switches (SW1, SW2), one 1941 router (R1), and four PCs in
Packet Tracer. Connected PC1 and PC2 to SW1, PC3 and PC4 to SW2, then connected
SW1 to R1 G0/0 and SW2 to R1 G0/1 using straight-through copper cables.

### Step 2 — Assign IP Addresses to End Devices
Configured each PC with a static IPv4 address and subnet mask. Default gateways
were intentionally left blank at this stage to observe unrouted traffic behaviour.

```
PC1  IP: 192.168.1.10   Mask: 255.255.255.0   GW: (none yet)
PC2  IP: 192.168.1.20   Mask: 255.255.255.0   GW: (none yet)
PC3  IP: 192.168.2.10   Mask: 255.255.255.0   GW: (none yet)
PC4  IP: 192.168.2.20   Mask: 255.255.255.0   GW: (none yet)
```

### Step 3 — Observe Unrouted Traffic
Used simulation mode to send ICMP packets between devices. Intra-LAN pings
succeeded. Cross-LAN pings timed out — confirming hosts cannot communicate
across networks without a router and default gateway.

```
C:\> ping 192.168.1.20   ← PC1 to PC2 (same LAN)     ✓ Success
C:\> ping 192.168.2.10   ← PC1 to PC3 (cross-LAN)    ✗ Request timed out
```

### Step 4 — Configure Router Interfaces
Accessed R1 via Cisco IOS CLI. Assigned IPs to both interfaces and brought
them up with `no shutdown`.

```
Router> enable
Router# configure terminal
Router(config)# hostname R1

R1(config)# interface FastEthernet0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface FastEthernet0/1
R1(config-if)# ip address 192.168.2.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# end
R1#  copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
```

### Step 5 — Set Default Gateways on All PCs
Updated each PC with the correct default gateway — the IP of the router
interface on their local LAN.

```
PC1  IP: 192.168.1.10   Mask: 255.255.255.0   GW: 192.168.1.1
PC2  IP: 192.168.1.20   Mask: 255.255.255.0   GW: 192.168.1.1
PC3  IP: 192.168.2.10   Mask: 255.255.255.0   GW: 192.168.2.1
PC4  IP: 192.168.2.20   Mask: 255.255.255.0   GW: 192.168.2.1
```

### Step 6 — Verify End-to-End Connectivity
All cross-LAN pings succeeded. Simulation mode confirmed the full path:
PC → Switch → Router → Switch → Destination.

```
C:\> ping 192.168.1.20   ← PC1 to PC2 (same LAN)    ✓
C:\> ping 192.168.2.10   ← PC1 to PC3 (cross-LAN)   ✓
C:\> ping 192.168.2.20   ← PC1 to PC4 (cross-LAN)   ✓

R1# show ip route
C    192.168.1.0/24 is directly connected, FastEthernet0/0
C    192.168.2.0/24 is directly connected, FastEthernet0/1
```

---

## 📊 Results

| # | Result | Status |
|---|---|---|
| 1 | Intra-LAN pings succeeded before and after router configuration | ✅ |
| 2 | Cross-LAN pings failed before router config — gateway required | ✅ |
| 3 | All cross-LAN pings succeeded after full configuration | ✅ |
| 4 | Simulation mode confirmed correct packet path end-to-end | ✅ |
| 5 | R1 G0/0 left in shutdown state on first attempt — pings still failed | ⚠️ |
| 6 | Fixed with `no shutdown` — interface came up, connectivity confirmed | 🔧 |

---

## 📚 Lessons Learned

**1. The default gateway is not optional**
Without a default gateway, a host drops any packet destined for a different
network before it ever reaches the router. Seeing this fail live made the
concept click in a way theory alone could not.

**2. Cisco interfaces are down by default**
Router interfaces are administratively shut down out of the box. Forgetting
`no shutdown` means the interface is correctly configured but completely
inactive — a common real-world mistake.

**3. Simulation mode is a powerful troubleshooting tool**
Watching packets step through the topology hop by hop made it immediately
obvious where traffic was dropping and why. This mental model applies directly
to real-world troubleshooting.

**4. What I would extend next time**
Replace static addressing with a DHCP server on R1, add a third LAN requiring
static routes, and layer Wireshark captures on top to inspect ARP and ICMP
at the frame level.

---

## 📁 Repository Structure

```
routed-network-traffic-flow/
├── index.html                                          ← Full project write-up (live via GitHub Pages)
├── README.md                                           ← This file
├── routed-network-traffic-flow-analysis.pkt            ← Cisco Packet Tracer project file
├── topology.png                                        ← Full topology screenshot
├── pings. png                                          ← Cross-LAN pings
└── route-table.png                                     ← R1 show ip route output
  
```

<!-- ✏️ Remove any files from the structure above that you haven't added yet -->

---

## 🔗 Links

- 📄 [Full Project Write-up](https://oziomainya.github.io/routed-network-traffic-flow-analysis)
- 🌐 [Portfolio](https://oziomainya.github.io)

<!-- ✏️ Replace [YOUR_GITHUB_USERNAME] and [YOUR_PORTFOLIO_URL] with your real details -->

---

*Ozioma Inya · [LinkedIn ↗](https://linkedin.com/in/ozioma-inya-a46327304) · [Github ↗](https://github.com/oziomainya)*
<!-- ✏️ Replace the placeholders above with your real details -->
