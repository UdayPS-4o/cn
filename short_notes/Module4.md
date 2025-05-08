# Module 4: Network Layer (Short Notes & Revision)

## Key Concepts & Exam Points

### 1. Network Layer: Core Responsibilities
*   **Primary Goal:** Logical addressing and routing of packets across different networks (source to destination inter-networking).
*   **Key Functions:**
    *   **Logical Addressing (IP Addresses):** Unique global identifiers (e.g., IPv4, IPv6).
    *   **Routing:** Path determination using routing algorithms (Dijkstra, Bellman-Ford).
    *   **Packet Forwarding:** Moving packets based on routing tables (longest prefix match).
    *   **Fragmentation & Reassembly:** Handling different network MTUs (Max Transmission Unit).
    *   **Services to Transport Layer:** Connectionless (e.g., IP - datagrams, unreliable) or Connection-Oriented (e.g., X.25 - virtual circuits, more reliable features).
*   **Design Issues:** Addressing scheme, routing algorithm choice, QoS, congestion, security, scalability.

### 2. Routing Algorithms
*   **Goal:** Find the "best" path (least cost, shortest, fastest).
*   **Categories:**
    *   **Static:** Manually configured. Simple, not adaptive.
    *   **Dynamic:** Routers exchange info, adaptive (RIP, OSPF, BGP).
    *   **Global (Link State - LS):** All routers know entire topology (e.g., Dijkstra). `OSPF` uses LS.
        *   **Dijkstra's:** Finds shortest path from one node to all others. Needs full topology. Non-negative link costs. Prone to oscillations if cost metric is dynamic load.
    *   **Decentralized (Distance Vector - DV):** Routers know only neighbors, get info from them (e.g., Bellman-Ford). `RIP` uses DV.
        *   **Bellman-Ford:** Each router tells neighbors its distance to destinations. Prone to **Count-to-Infinity** problem (bad news travels slow). Solutions: Split Horizon, Poison Reverse, Hold-down timers.
*   **Hierarchical Routing:** Divides network into Autonomous Systems (AS). IGP within AS (OSPF, RIP), EGP between ASes (BGP). Improves scalability.
*   **Broadcast Routing:** Send to all nodes.
    *   Methods: Flooding (controlled by sequence # or TTL), Spanning Tree, **Reverse Path Forwarding (RPF)** (preferred: forward if packet arrived on shortest path from source).
*   **Multicast Routing:** Send to a group of interested nodes (multicast group address - Class D).
    *   Uses IGMP for group membership.
    *   Protocols: DVMRP (flood & prune), MOSPF (OSPF extension), PIM (Sparse Mode for widespread groups, Dense Mode for concentrated groups).

### 3. IP Addressing (IPv4 Focus)
*   **IPv4 Address:** 32-bit, dotted decimal (e.g., `192.168.1.10`). Network ID + Host ID.
*   **Classes (Historical):**
    *   **A (0...):** Large nets. /8 default mask.
    *   **B (10..):** Medium nets. /16 default mask.
    *   **C (110.):** Small nets. /24 default mask.
    *   **D (1110):** Multicast.
    *   **E (1111):** Experimental.
*   **Special Addresses:**
    *   Network Address: Host bits = 0 (e.g., `192.168.1.0`)
    *   Directed Broadcast: Host bits = 1 (e.g., `192.168.1.255`)
    *   Limited Broadcast: `255.255.255.255` (local net)
    *   Loopback: `127.0.0.1`
    *   Private IPs: `10.x.x.x`, `172.16.x.x-172.31.x.x`, `192.168.x.x` (Need NAT for internet).
*   **Subnetting:** Divide a network into smaller sub-networks. Borrows host bits for subnet ID. Uses a **subnet mask** (e.g., `255.255.255.192` or `/26`).
    *   *Exam Tip: Given an IP and mask, find Network ID, Broadcast Addr, valid host range, number of subnets/hosts.*
*   **CIDR (Classless Inter-Domain Routing):** `a.b.c.d/x` (x = prefix length). Allows variable length prefixes. Solves address exhaustion, enables route summarization.

### 4. IPv4 Header Format (Key Fields for Exam)
```
|Ver|IHL| ToS | Total Length |
| Identification |Flg| Fragment Offset |
| TTL | Proto | Header Checksum |
| Source IP Address |
| Destination IP Address |
| Options (rarely critical for basic Qs) |
```
*   **Version (4):** Always 4 for IPv4.
*   **IHL (Header Length):** In 32-bit words (min 5, so 20 bytes).
*   **Total Length:** Header + Data (bytes).
*   **Identification, Flags (DF, MF), Fragment Offset:** Used for **Fragmentation**.
    *   **DF (Don't Fragment):** If 1, drop if too big & send ICMP error.
    *   **MF (More Fragments):** If 1, more frags follow. If 0, last/only frag.
    *   **Offset:** Position of frag data in original datagram (in 8-byte units).
*   **TTL (Time To Live):** Decremented by each router. Discard if 0 (prevents loops).
*   **Protocol:** Next layer (TCP=6, UDP=17, ICMP=1).
*   **Header Checksum:** For header error detection. Recalculated by routers (TTL changes).
*   **Source/Destination IP:** 32-bit addresses.

### 5. Packet Forwarding & Fragmentation
*   **Forwarding:** Router uses **longest prefix match** in routing table to find outgoing interface/next hop.
*   **Fragmentation:** If packet size > MTU of outgoing link.
    *   **IPv4:** Routers *can* fragment. Reassembly *only* at destination host.
    *   **Fields used:** Identification, Flags, Fragment Offset.
    *   *Remember calculation: Max data in frag = MTU - IP_Header_Size. Ensure data length is multiple of 8 bytes for offset calculation.*
*   **Path MTU Discovery (PMTUD):** Avoids fragmentation by finding smallest MTU in path. Uses DF bit and ICMP "Frag Needed" error.

### 6. ICMP (Internet Control Message Protocol)
*   **Purpose:** Error reporting & network diagnostics for IP. Encapsulated in IP (Proto=1).
*   **Key Messages (Type, Code):**
    *   **Echo Request/Reply (8/0, 0/0):** `ping` utility.
    *   **Destination Unreachable (Type 3):**
        *   Network Unreachable (Code 0)
        *   Host Unreachable (Code 1)
        *   Protocol Unreachable (Code 2)
        *   Port Unreachable (Code 3) - *Crucial for UDP*
        *   Fragmentation Needed & DF Set (Code 4) - *For PMTUD*
    *   **Time Exceeded (Type 11):**
        *   TTL Expired (Code 0) - *Used by `traceroute`*
        *   Fragment Reassembly Time Exceeded (Code 1)
    *   **Redirect (Type 5):** Router tells host about a better first-hop for a destination.
    *   **Source Quench (Type 4):** Congestion signal (deprecated).
*   *Exam Tip: ICMP error messages include IP header + first 8 bytes of data of the error-causing packet.*
*   *ICMP does NOT make IP reliable.*

### 7. IPv4 vs. IPv6 Comparison

| Feature             | IPv4                      | IPv6                                   |
|---------------------|---------------------------|----------------------------------------|
| **Address Size**    | 32-bit                    | 128-bit                                |
| **Notation**        | Dotted Decimal            | Hexadecimal, colon-separated           |
| **Header**          | 20-60 bytes, complex opts | 40 bytes fixed, extension headers      |
| **Checksum**        | In header                 | No header checksum (upper layers)      |
| **Fragmentation**   | Routers & Host            | Only Host (PMTUD essential)            |
| **Config**          | Manual/DHCPv4             | SLAAC, DHCPv6                          |
| **IPsec**           | Optional                  | Integrated                             |
| **Broadcast**       | Yes                       | No (uses Multicast)                    |
| **Anycast**         | Limited                   | Native support                         |
| **Key Benefit**     | Established               | **Huge Address Space**, Simpler Header, Better Security & QoS |

**Remember IPv6 Advantages for Exams:**
1.  **Massive Address Space:** Solves IPv4 exhaustion.
2.  **Simplified Header:** Faster router processing (fixed length, no checksum).
3.  **End-to-End Connectivity:** NAT becomes less necessary, facilitating true peer-to-peer.
4.  **Built-in Security (IPsec):** Though implementation varies.
5.  **Stateless Address Autoconfiguration (SLAAC):** Easier host configuration.
6.  **Better QoS/Flow Labeling.**

## Mnemonics & Tips

*   **Routing Algorithm Types - "Leaders Don't Follow":**
    *   **L**ink **S**tate (Dijkstra) - Global info, knows the *Leader's* map.
    *   **D**istance **V**ector (Bellman-Ford) - Local info, *Follows* neighbors' advice.
*   **IPv4 Header Fields (Order for common ones):** "**V**ery **I**ntelligent **T**ourists **L**ove **Id**entifying **F**lags & **F**ragments, **T**hen **P**roceed to **C**heck **S**ource & **D**estination."
    *   **V**ersion, **IH**L, **T**oS, Total **L**ength, **Id**entification, **Fl**ags, **F**ragment Offset, **TT**L, **P**rotocol, Header **C**hecksum, **S**ource IP, **D**estination IP.
*   **Dijkstra vs. Bellman-Ford:**
    *   Dijkstra: Good for stable networks where full map is known. Like GPS with full map data.
    *   Bellman-Ford: Good for changing networks, simpler router logic. Like asking for directions at each junction.
*   **ICMP means "I See Message (Problem/Ping)":** Helps remember its for control/error messages.
*   **Fragmentation:** Think of a large letter (datagram) that needs to be put into several small envelopes (fragments) to fit through a mail slot (MTU). Each envelope needs its own address (header) and info about which part of the letter it is (Ident, MF, Offset).

By focusing on these key areas, understanding the purpose of each protocol/algorithm, and practicing with examples (especially subnetting and fragmentation), you can effectively prepare for exam questions on Module 4. 