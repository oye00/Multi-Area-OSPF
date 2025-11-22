📋 Project Overview

This project demonstrates the configuration and simulation of a multi-area OSPF (Open Shortest Path First) network using Cisco Packet Tracer.
The goal is to design a hierarchical OSPF topology that improves routing scalability, reduces overhead, and simulates a real-world enterprise or ISP network structure.

The topology consists of 7 routers organized across 3 OSPF areas, with one router acting as an Autonomous System Boundary Router (ASBR) that connects the internal OSPF network to an external ISP.

<img width="1407" height="726" alt="network topology" src="https://github.com/user-attachments/assets/25e5ae2f-3e06-4822-9f9e-63da9a360c81" />

🧭 Network Topology Overview

Areas:

Area 0 (Backbone): Central routing core; connects all other areas.

Area 1: Handles local routing for a subset of internal networks (branch 1).

Area 2: Handles local routing for another subset (branch 2).

Router Roles:

ABR (Area Border Router):
Routers that connect the backbone (Area 0) to another OSPF area.
They summarize and share routes between areas to reduce routing table size.

Router2 (ABR1): Connects Area 0 ↔ Area 1

Router3 (ABR2): Connects Area 0 ↔ Area 2

ASBR (Autonomous System Boundary Router):
A router that connects the OSPF domain to an external network (like an ISP).
It redistributes external routes (static or from another protocol) into the OSPF process.

Router1 (ASBR): Connects Area 0 ↔ ISP (external network)

Internal Routers:
Routers fully contained within one OSPF area (e.g., Router4, Router5, Router6, Router7).


⚙️ Implementation Steps
1️⃣ IP Addressing and Interface Configuration

Assign IP addresses according to the topology diagram (network topology.png).
Here’s an example for Router2 (ABR1):

hostname Router2
interface g0/0
ip address 10.10.10.2 255.255.255.252
no shutdown
interface g0/1
ip address 192.168.1.1 255.255.255.0
no shutdown

Repeat for each router using your IP plan.


2️⃣ Default Route Between ASBR and ISP

On ISP router:

ip route 0.0.0.0 0.0.0.0 s0/3/0


On ISP Router:

router ospf 1
default-information originate
router-id 5.5.5.5
network 10.10.10.0 0.0.0.3 area 0
network 10.10.10.4 0.0.0.3 area 0

➡️ This static default route tells the ASBR that any unknown destination should be forwarded to the ISP router.

============================================================================================================================================================================================================================================

3️⃣Configure OSPF Routing

Enable OSPF on all routers and assign networks to their respective areas.

Example — Router2 (ABR1)
router ospf 1
 router-id 2.2.2.2
 network 10.10.10.0 0.0.0.3 area 0
 network 192.168.1.0 0.0.0.255 area 1

Example — Router3 (ABR2)
router ospf 1
 router-id 3.3.3.3
 network 10.10.10.4 0.0.0.3 area 0
 network 192.168.50.0 0.0.0.255 area 2

Repeat for all the other routers participating in OSPF

============================================================================================================================================================================================================================================

4️⃣ Verify OSPF Adjacencies

Use the following commands on each router:

show ip ospf neighbor
<img width="797" height="111" alt="show IP ospf neighbor (router 1_ASBR)" src="https://github.com/user-attachments/assets/3ecb92af-3f4f-4892-bc2b-5c2b7aa8c467" />

show ip ospf interface brief
<img width="981" height="118" alt="ospf brief interface_router_2" src="https://github.com/user-attachments/assets/06f981f9-1ca4-47dd-83d0-c4bb1284afe7" />

show ip route ospf
<img width="785" height="173" alt="show ip route OSPF_router_4" src="https://github.com/user-attachments/assets/d1ba97fb-a465-4091-b5a4-1f5ea8d1cf1c" />


✅ All routers within the same area should form neighbor adjacencies.
✅ Routes from all areas should appear in each router’s routing table.

============================================================================================================================================================================================================================================

5️⃣ Test Network Connectivity

Use ping and traceroute:
<img width="1102" height="111" alt="ping_router_5_from_router_7" src="https://github.com/user-attachments/assets/47089084-c67f-454a-9953-9ee94b43ef49" />


traceroute 192.168.52.1


You should have full connectivity between hosts in all areas (Area 1 ↔ Area 2).

============================================================================================================================================================================================================================================

IP Address Summary Table

| Router                 | Interface | IP Address        | Area     | Role                |
| ---------------------- | --------- | ----------------- | -------- | ------------------- |
| **Router1 (ASBR)**     | g0/0      | 10.10.10.1/30     | 0        | Backbone            |
|                        | g0/1      | 10.10.10.5/30     | 0        | Backbone            |
|                        | s0/3/0    | 200.143.113.10/29 | External | ISP Link            |
| **Router2 (ABR1)**     | g0/0      | 10.10.10.2/30     | 0        | Connects to ASBR    |
|                        | g0/1      | 192.168.1.1/24    | 1        | Connects to Router4 |
| **Router3 (ABR2)**     | g0/1      | 10.10.10.6/30     | 0        | Connects to ASBR    |
|                        | g0/2      | 192.168.50.1/24   | 2        | Connects to Router6 |
| **Router4 (Internal)** | g0/1      | 192.168.1.2/24    | 1        | Connects to Router2 |
|                        | g0/2      | 192.168.2.1/24    | 1        | Connects to Router5 |
| **Router5 (Internal)** | g0/2      | 192.168.3.1/24    | 1        | Connects to Router4 |
| **Router6 (Internal)** | g0/0      | 192.168.51.1/24   | 2        | Connects to Router7 |
|                        | g0/2      | 192.168.50.2/24   | 2        | Connects to Router3 |
| **Router7 (Internal)** | g0/0      | 192.168.52.1/24   | 2        | Connects to Router6 |
| **ISP**                | s0/3/0    | 200.143.113.9/29  | —        | External Network    |


