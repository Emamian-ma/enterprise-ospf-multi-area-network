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

## 📋 IP Addressing Plan

| Device | Interface | IP Address | Area | Description |
| :--- | :--- | :--- | :--- | :--- |
| **R1 (HQ)** | Lo0 | `1.1.1.1/32` | 0 | Loopback / Router-ID |
| **R4 (BR1)** | Gi0/1.10 | `192.168.10.1/24` | 10 | Gateway - Engineering |
| **R4 (BR1)** | Gi0/1.20 | `192.168.20.1/24` | 10 | Gateway - Finance |
| **R5 (BR2)** | Gi0/1.30 | `192.168.30.1/24` | 20 | Gateway - Sales |
| **R5 (BR2)** | Gi0/1.40 | `192.168.40.1/24` | 20 | Gateway - Support |

## 🔍 Technical Deep Dive

### 1. Route Summarization Strategy
To keep the **Area 0 Link-State Database (LSDB)** lean, summarization is performed on `R2` and `R3` (ABRs).
- **R2 (ABR):** Summarizes `192.168.10.0/24` and `192.168.20.0/24` into `192.168.10.0/23`.
- **Benefit:** If a VLAN interface flaps in Branch 1, Area 0 routers don't receive a new LSA, preventing unnecessary SPF calculations.

### 2. Router-on-a-Stick (802.1Q)
Instead of using multiple physical interfaces for each VLAN, we used **Sub-interfaces** on `R4` and `R5`. 
- **Configuration:** Encapsulation dot1q is used to tag traffic for VLANs 10, 20, 30, and 40.
- **Switching:** `SW-BR1` and `SW-BR2` act as Layer 2 devices with trunk ports facing the routers.

## 🚀 How to Use this Lab
1. **Requirements:** GNS3 installed with Cisco IOS images (e.g., vIOS or 7200).
2. **Topology:** Import the image from `/topology` to recreate the link structure.
3. **Configuration:** 
   - Apply the `.txt` files from the `/configs` folder to each respective device.
   - Ensure the VPCS (Virtual PCs) are configured with the IPs mentioned in the IP Plan.
4. **Testing:** Run the commands in the *Verification* section to ensure full convergence.

## 🛠️ Troubleshooting & Challenges
- **OSPF Neighbor Adjacency:** Initially, neighbors didn't form due to MTU mismatch on serial links. *Solution:* Adjusted MTU or used `ip ospf mtu-ignore`.
- **VLAN Routing:** Ensure the switch port connecting to the router is in `trunk` mode, otherwise, sub-interfaces will remain down.

## 📁 Project Structure
```text
.
├── configs/           # Device configuration files (.txt)
├── topology/          # GNS3 Topology diagrams and screenshots
├── LICENSE            # MIT License
└── README.md          # Project documentation
