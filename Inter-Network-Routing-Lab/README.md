# Inter-Network Routing Lab

A Packet Tracer lab demonstrating Inter-VLAN routing utilizing a **Router-on-a-Stick (ROAS)** architecture at local sites, combined with static/dynamic routing across a wide area network (WAN) link between two separate locations.

## Overview
This lab simulates two separate office sites (Site 1 and Site 2), each utilizing VLAN segmentation for local traffic isolation. Instead of routing traffic locally on the switches, each site deploys an edge router to perform Inter-VLAN routing via virtual subinterfaces. The two sites are linked via a dedicated point-to-point network backbone.

---

## Network Architecture & Addressing

### Site 1 (Left Side - Headquarters)
* **Local Switch:** `SWT1` (Layer 2 Switch)
* **Gateway Router:** `R1` (Performs Router-on-a-Stick via `G0/0`)

| Device | VLAN | IP Address | Subnet Mask | Role / Department |
| :--- | :--- | :--- | :--- | :--- |
| **PC1** | VLAN 10 | 192.168.10.10 | 255.255.255.0 | Sales / General |
| **PC2** | VLAN 20 | 192.168.20.20 | 255.255.255.0 | Engineering / IT |
| **S1** | — | DHCP / Static | — | Local Server 1 |

### Site 2 (Right Side - Branch Office)
* **Local Switch:** `SWT2` (Layer 2 Switch)
* **Gateway Router:** `R2` (Performs Router-on-a-Stick via `G0/0`)

| Device | VLAN | IP Address | Subnet Mask | Role / Department |
| :--- | :--- | :--- | :--- | :--- |
| **PC3** | VLAN 10 | 192.168.11.2 | 255.255.255.0 | Sales / General (Branch) |
| **PC4** | VLAN 20 | 192.168.21.1 | 255.255.255.0 | Engineering / IT (Branch) |
| **S2** | — | DHCP / Static | — | Local Server 2 |

### Core Transit Backbone (WAN Link)
| Network | Subnet | Purpose |
| :--- | :--- | :--- |
| **Link 1** | 192.168.1.0/24 | Transit network exiting R1 (`G0/1`) |
| **Link 2** | 192.168.2.0/24 | Transit network entering R2 (`G0/1`) |

---

## Configuration Highlights

### 1. Inter-VLAN Routing (Router-on-a-Stick)
Because standard Layer 2 switches (`SWT1` & `SWT2`) cannot route packets between different subnets, **Router-on-a-Stick** is implemented:
* The switch ports connecting to the routers (`SWT1 G0/1` and `SWT2 G0/1`) are configured as **Trunk Ports** (`switchport mode trunk`) to carry both VLAN 10 and VLAN 20 tagged traffic.
* The router interfaces (`R1 G0/0` and `R2 G0/0`) are carved into logical **subinterfaces** (e.g., `G0/0.10` and `G0/0.20`).
* Each subinterface uses `encapsulation dot1Q <vlan_id>` to handle IEEE 802.1Q tags and serves as the **Default Gateway** for its respective local subnet.

### 2. Inter-Network Routing (WAN)
To allow traffic to leave Site 1 and reach Site 2 across the backbone, routers **R1** and **R2** are configured with routing table entries (Static Routes or Dynamic Routing Protocols like OSPF) to map out remote destinations across the `192.168.1.0` and `192.168.2.0` links.

---

## Traffic Flow Examples

### Scenario A: PC1 pings PC2 (Local Inter-VLAN Traffic)
1. **PC1** sends an ICMP packet destined for `192.168.20.20` (PC2). It realizes the target is on a different subnet and forwards it to its default gateway (`192.168.10.1` on `R1`).
2. The packet enters **SWT1** on an access port, gets tagged as **VLAN 10**, and rides the trunk link up to **R1**.
3. **R1** receives the packet on subinterface `G0/0.10`, inspects the Layer 3 header, and routes it out of subinterface `G0/0.20` tagged as **VLAN 20**.
4. The packet goes back down the trunk to **SWT1**, which strips the tag and delivers it to **PC2**.

### Scenario B: PC1 (Site 1) pings PC3 (Site 2)
1. Packet goes up the local trunk to **R1** (`G0/0.10`).
2. **R1** checks its routing table for the `192.168.11.0/24` network and matches the route pointing across the WAN link (`G0/1`).
3. The packet traverses the core network to **R2**.
4. **R2** accepts the packet, routes it downward through its local subinterface (`G0/0.10`), down the trunk link, and across **SWT2** to reach **PC3**.

---

## Core Skills Demonstrated
* **VLAN Segmentation:** Grouping end-devices into isolated Layer 2 broadcast domains.
* **Trunking (802.1Q):** Transporting multiple VLAN traffic over a single physical medium.
* **Router-on-a-Stick (ROAS):** Provisioning logical router subinterfaces for capital-efficient routing.
* **Multi-Router Topologies:** Navigating packets across point-to-point serial or Ethernet WAN circuits.
