# CMPG 325 - Network Design Project

## Project Details
* **Student:** Makhoba, Coby
* **Student Number:** 46967702
* **Project ID:** CMPG325-2026-039
* **Client ID:** CLI-039
* **Assigned Organisation:** Ditso Dairy Farm (Bloemhof)
* **Industry:** Agriculture

## Client Overview
This repository contains the network design and Cisco Packet Tracer simulation for Ditso Dairy Farm, a working agricultural operation located in Bloemhof. The network provisions connectivity for administrative offices, farm operations, management infrastructure, and guest access, strictly adhering to the client's operational context.

## Network Design & Topology
The network utilizes a **Hierarchical Extended Star** physical topology and a **Router-on-a-Stick** logical topology. 
* **Core Layer:** Redundant Layer 3 switches to ensure high availability.
* **Access Layer:** Dedicated Layer 2 switches for specific operational areas (Admin, Farm Ops, Guest, New Area).

## IP Addressing Plan
The network is subnetted from the assigned **10.21.0.0/16** block. Fixed /24 subnets are utilized to establish clear segment boundaries and provide substantial headroom for future agricultural sensor network expansion.

| VLAN | Segment | Subnet | Gateway |
| :--- | :--- | :--- | :--- |
| 10 | ADMIN | 10.21.10.0/24 | 10.21.10.1 |
| 20 | FARM-OPS | 10.21.20.0/24 | 10.21.20.1 |
| 30 | MGMT-SERVERS| 10.21.30.0/24 | 10.21.30.1 |
| 40 | GUEST | 10.21.40.0/24 | 10.21.40.1 |
| 50 | NEW-AREA | 10.21.50.0/24 | 10.21.50.1 |

## Assigned Technical Challenge: VLANs
The network demonstrates an intermediate switch-based segmentation design. Traffic is logically separated into distinct broadcast domains (VLANs 10, 20, 30, 40, 50) using 802.1Q trunking and Inter-VLAN routing via the default gateway router.

## Constraints & Change Requests
* **Constraint:** *No network downtime tolerated during month-end processing.* Addressed via redundant EtherChannel links between core switches, STP failover, and strict VLAN isolation for the Admin subnet.
* **Change Request (CR2):** *Client takes over an additional floor/area.* Accommodated by extending a new access switch (`SW-NEWAREA`) and provisioning VLAN 50, integrating seamlessly without redesigning the existing baseline.
