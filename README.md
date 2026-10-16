# Ditso Dairy Farm - Enterprise Network Architecture

## 📄 Final Technical Report
[**Read the Full CMPG 325 Technical Report (PDF)**](CMPG325_Final_Technical_Report.pdf)

## 1. Project Overview
This repository contains the network design, Packet Tracer simulation, and testing evidence for the Ditso Dairy Farm (Bloemhof) enterprise network. The objective was to design a scalable, segmented network infrastructure that isolates distinct agricultural functions (Administration, Farm Operations, Guest Access, and Management) into separate broadcast domains while ensuring secure inter-VLAN routing and automated IP addressing.

### Client Requirements & Constraints
* **Industry:** Agriculture (Working Dairy Operation)
* **Assigned Technical Challenge:** Intermediate switch-based VLAN segmentation.
* **Design Constraint:** No network downtime tolerated during month-end processing (Billing/Admin).
* **Change Request (CR2):** The client takes over an additional floor/area and requires immediate network coverage without redesigning the core architecture.

---

## 2. Network Topology & IP Addressing
The network utilizes an **Extended Star Topology** centered around a Cisco 4321 Router (R1) and a 2960 Core Switch, branching out to dedicated access switches for each physical area. 

**Subnetting Plan (Assigned Block: 10.21.0.0/16)**
| VLAN | Segment Name | Network Subnet | Default Gateway | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **10** | ADMIN | `10.21.10.0/24` | `10.21.10.1` | Office, billing, and month-end records (DHCP) |
| **20** | FARM-OPS | `10.21.20.0/24` | `10.21.20.1` | Milking parlour systems and herd terminals (DHCP) |
| **30** | MGMT-SERVERS | `10.21.30.0/24` | `10.21.30.1` | Farm management server and IT admin PC (Static) |
| **40** | GUEST | `10.21.40.0/24` | `10.21.40.1` | Visitor and vendor network access (DHCP) |
| **50** | NEW-AREA-CR2 | `10.21.50.0/24` | `10.21.50.1` | Coverage for additional floor/area (DHCP) |
| **99** | NATIVE-MGMT | `10.21.99.0/24` | `10.21.99.1` | Switch management infrastructure |

---

## 3. Configuration Highlights
* **VLAN Segmentation:** Grouped specific switch ports to isolate agricultural department traffic, completely separating the Admin broadcast domain from Farm-Ops and Guests.
* **Inter-VLAN Routing (Router-on-a-Stick):** Configured 802.1Q encapsulation on R1 virtual sub-interfaces (`g0/0/0.10` through `g0/0/0.99`) to route traffic centrally.
* **DHCP Services:** Engineered router-based DHCP pools on R1 to automatically provision IP addresses to end devices, explicitly excluding the `.1` default gateway addresses.
* **Spanning Tree Protocol (STP):** Configured PortFast and BPDU Guard on all access-layer ports. This satisfies the critical design constraint by instantly disabling ports if a switching loop is detected, preventing accidental downtime during month-end processing.
* **Scalability (CR2):** Addressed the client change request by attaching `SW-NEWAREA` to the core switch and assigning it VLAN 50. The extended-star topology allowed this expansion without altering the existing IP or routing structures.

---

## 4. Troubleshooting Log
During the implementation phase, the following issues were identified and resolved:
1. **DHCP Broadcast Failure:** End devices initially failed to pull IP configurations. Investigation revealed that the `SW-CORE` uplink ports were misconfigured as access ports for VLAN 30. The ports were reconfigured to `switchport mode trunk` with Native VLAN 99, restoring the path for DHCP broadcasts to reach R1.
2. **Err-Disabled Trunk Links:** Switch-to-switch connections dropped Layer 1 connectivity (red link lights). This was caused by mistakenly applying BPDU Guard to the 802.1Q trunk ports. BPDU Guard was removed from the uplinks, and the interfaces were reset using `shutdown` and `no shutdown`, restoring full connectivity.

---

## 5. Testing & Verification Evidence
*(The following screenshots confirm the successful operation of the assigned networking challenges).*

**Inter-VLAN Routing (Ping Test):**
Successful ICMP echo replies across disparate VLANs (Admin PC communicating with Management Server).
![Routing Proof](documentation/Ping_test.png)

**Automated DHCP Allocation:**
End devices successfully leasing `10.21.X.X` configurations from the R1 DHCP pools.
![DHCP Proof](images/dhcp_lease.png)

**Switch Trunking & Routing Tables:**
Verification of 802.1Q encapsulation on SW-CORE and active connected routes on R1.
![Trunking Proof](images/trunk_status.png)
![Routing Table Proof](images/routing_table.png)

---

## 6. Project Reflection
This project successfully balances the Ditso Dairy Farm's need for strict operational security with straightforward connectivity. By utilizing switch-based VLAN segmentation and Spanning Tree edge-port protections, the design logically secures the administrative network without requiring expensive hardware upgrades. The modular nature of the architecture proved its value immediately by easily accommodating the CR2 facility expansion, resulting in a stable, fully routed infrastructure tailored directly to the client's agricultural operations.
