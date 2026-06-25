# Enterprise OSPF Multi-Area Network (GNS3 Lab)

## 📌 Project Overview
This project showcases a scalable **Enterprise WAN architecture** designed and simulated in **GNS3**. The network implements a hierarchical **OSPF Multi-Area** design, optimized for branch connectivity and secure inter-area routing. By utilizing **Route Summarization** at the Distribution/Core layer and **Router-on-a-Stick** at the branch access layer, this lab demonstrates enterprise best practices for network modularity and efficiency.

## 🏗️ Network Architecture
- **Area 0 (Backbone):** Functions as the core transit area connecting the Headquarters (`R1`) and Branch ABRs (`R2`, `R3`).
- **Area 10 (Branch 1):** Connects the Engineering and Finance departments via `R4`.
- **Area 20 (Branch 2):** Connects the Sales and Support departments via `R5`.

### Key Technical Implementations:
*   **Hierarchical Design:** Multi-area OSPF structure to localize Link State Advertisements (LSAs) and reduce overhead.
*   **Route Summarization:** Configured on ABRs (`R2` & `R3`) to aggregate branch subnets (`/24`) into larger summary routes (`/23`), minimizing routing table size in Area 0.
*   **Router-on-a-Stick (RoS):** Efficient 802.1Q trunking implementation on branch edge routers, terminating multiple VLANs on a single physical interface.
*   **Security Hardening:** Implementation of `passive-interface` on all user-facing subnets and loopback interfaces to prevent unauthorized OSPF adjacencies.

## 📊 IP Addressing & Routing Summary
| Area | Summary Route | Description |
| :--- | :--- | :--- |
| Area 10 | `192.168.10.0/23` | Summarizes Branch 1 (VLAN 10 & 20) |
| Area 20 | `192.168.30.0/23` | Summarizes Branch 2 (VLAN 30 & 40) |

## 🚀 Verification Procedures
To ensure network health and convergence, the following verification suite is utilized:
```bash
# Verify OSPF Adjacency
show ip ospf neighbor

# Inspect Routing Table for OSPF routes
show ip route ospf

# View the OSPF Database (LSDB)
show ip ospf database

# Monitor OSPF interface states
show ip ospf interface brief

# Test end-to-end reachability (Source: Branch 1 / Dest: Branch 2)
ping 192.168.40.10 source 192.168.10.1

