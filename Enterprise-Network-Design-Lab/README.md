Network Topology Lab
A Packet Tracer lab demonstrating a multi-VLAN enterprise network with inter-VLAN routing, DHCP services, and WAN connectivity.

Overview
This lab simulates a small-to-medium enterprise network divided into multiple departments using VLANs. The network includes a Layer 3 switch for inter-VLAN routing, a dedicated DHCP server for automatic IP addressing, and a simulated WAN connection to demonstrate internet-bound traffic.

Network Architecture
| VLAN    | Department  | Purpose                                 |
| ------- | ----------- | --------------------------------------- |
| VLAN 10 | Sales       | Sales department workstations           |
| VLAN 20 | IT          | IT department workstations              |
| VLAN 30 | Engineering | Engineering department workstations     |
| VLAN 40 | HR          | Human Resources workstations            |
| VLAN 50 | DHCP        | Dedicated DHCP server for IP allocation |
| VLAN 99 | —           | **Native VLAN** (untagged traffic)      |

Key Components
| Device                   | Role                                                          |
| ------------------------ | ------------------------------------------------------------- |
| **Layer 2 Switch**       | Access layer — connects end devices to their respective VLANs |
| **Layer 3 Switch (SWT)** | Distribution/Core layer — handles inter-VLAN routing via SVIs |
| **DHCP Server**          | Provides dynamic IP addressing for all VLANs                  |
| **Left Router**          | Edge router — connects the LAN to the simulated Internet      |
| **Internet Cloud**       | Simulates the public Internet backbone                        |
| **Right Router**         | Remote edge — connects the Internet to the Cisco Server       |
| **Cisco Server**         | Simulated external server (e.g., web service on the Internet) |

Configuration Highlights
1. Trunking with Native VLAN
The link between the Layer 2 access switch and the Layer 3 switch (Gig0/1) is configured as an 802.1Q trunk:
Port     Mode    Encapsulation    Status      Native VLAN
Gig0/1   on      802.1q           trunking    99

* All VLANs (10, 20, 30, 40, 50) are allowed on the trunk
* Native VLAN 99: Any untagged Ethernet frames passing through this trunk will be associated with VLAN 99
* Tagged frames for VLANs 10–50 are properly identified and forwarded to their respective SVIs on the Layer 3 switch
2. Inter-VLAN Routing
* Configured on the Layer 3 switch using Switched Virtual Interfaces (SVIs)
* Each VLAN has its own SVI with an IP address serving as the default gateway for that subnet
* Enables communication between all internal VLANs without traffic leaving the local network
3. DHCP Server
* A dedicated server resides in VLAN 50
* Provides automatic IP address allocation for all VLANs via DHCP relay (ip helper-address) configured on each SVI
* Eliminates the need for static IP configuration on end devices
4. WAN Connectivity
* The left router connects the internal network to a simulated Internet cloud
* Static routes direct traffic destined for external networks (e.g., 3.3.3.0/24) toward the Internet
* NAT/PAT (Network Address Translation) translates private internal IPs to the public IP 1.1.1.2 for outbound traffic
* A Cisco Server on the remote side simulates an external web service accessible over the Internet

Traffic Flow Example: PC1 Accessing the Cisco Server
| Step | Action                                                                       |
| ---- | ---------------------------------------------------------------------------- |
| 1    | PC1 (VLAN 10) sends an HTTP request to `http://3.3.3.2`                      |
| 2    | Traffic reaches the Layer 3 switch via the access port (tagged VLAN 10)      |
| 3    | The Layer 3 switch routes the packet to its default route (left router)      |
| 4    | The left router performs NAT, translating the private source IP to `1.1.1.2` |
| 5    | The packet traverses the simulated Internet to the right router              |
| 6    | The right router forwards the packet to the Cisco Server at `3.3.3.2`        |
| 7    | The server responds, and the return traffic follows the reverse path         |

What This Lab Demonstrates
* VLAN segmentation for departmental isolation
* 802.1Q trunking with a dedicated native VLAN
* Inter-VLAN routing at the distribution layer
* DHCP services with relay for centralized IP management
* NAT/PAT for private-to-public IP translation
* Static routing for WAN connectivity
* End-to-end communication from LAN to simulated WAN (like accessing YouTube, Facebook, or any external server)
