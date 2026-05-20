# Enterprise Network Design Lab

A Packet Tracer lab demonstrating a resilient multi-VLAN enterprise campus network featuring centralized Multi-Layer Switch (MLS) routing, dedicated DHCP relay services, Network Address Translation (NAT), and edge WAN connectivity.

## Overview
This lab simulates an enterprise environment segmented into five functional VLANs. Local inter-VLAN routing is handled efficiently at the core/distribution layer using a Multi-Layer Switch (MLS), freeing up the edge router to focus purely on internet-bound traffic, NAT translation, and WAN routing to reach an external corporate server.

---

## Network Architecture & VLAN Matrix

| VLAN | Department | Subnet / Purpose | Gateway Device |
| :--- | :--- | :--- | :--- |
| **VLAN 10** | Sales | Sales Workstations (PC1, PC2) | MLS SVI |
| **VLAN 20** | IT | IT Operations (PC3, PC4, PC5) | MLS SVI |
| **VLAN 30** | Engineering| Engineering Workstations (PC6, PC7, PC8) | MLS SVI |
| **VLAN 40** | HR | Human Resources (PC9, PC10) | MLS SVI |
| **VLAN 50** | DHCP | Centralized DHCP Server Node | MLS SVI |
| **VLAN 99** | Native | Untagged Management & Control Traffic | — |

---

## Key Components

### Campus LAN (Left Side)
* **Layer 2 Access Switch (`SW`):** Aggregates all departmental end-devices into their respective access VLAN ports.
* **Multi-Layer Switch (`MLS`):** Serves as the core/distribution layer. Handles high-speed Inter-VLAN routing locally using Switched Virtual Interfaces (SVIs).
* **DHCP Server (VLAN 50):** Dynamically provisions IP addresses across all subnets utilizing `ip helper-address` (DHCP Relay) configured on the MLS SVIs.

### WAN / Internet Core (Center & Right)
* **Edge Router (`R1`):** Boundary router connecting the private campus LAN to the public internet network. Performs NAT/PAT translations.
* **Core Router (`INTERNET`):** Simulates the public Internet Service Provider (ISP) routing backbone.
* **Remote Edge Router (`R2`):** Boundary router routing public traffic directly to the target external data center.
* **Cisco Server:** A remote destination web server simulating public cloud or web services.

---

## Configuration Highlights

### 1. High-Speed Core Trunking
The uplink between the Access Switch (`SW`) and the Multi-Layer Switch (`MLS`) is bundled as an **802.1Q Trunk Link** (`G0/1`). 
* Explicitly allows VLANs 10, 20, 30, 40, and 50.
* Hardcoded to use **Native VLAN 99** for enhanced security posture against VLAN hopping exploits.

### 2. Edge NAT/PAT & Static WAN Routing
* **Inside Private Subnets:** All internal corporate networks utilize RFC 1918 private IP space.
* **NAT/PAT Execution:** As packets exit `R1` towards the `INTERNET` router, `R1` dynamically translates private source IPs into its routable public IP address (`1.1.1.2`).
* **Static Routing:** Default routes (`0.0.0.0 0.0.0.0`) push unrecognized enterprise traffic directly out toward the ISP backbone.

---

## End-to-End Traffic Flow Example
### Scenario: PC1 (VLAN 10) requests web content from Cisco Server via Public IP
