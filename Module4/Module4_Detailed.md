# Module 4: Network Layer (Detailed Notes)

## 1. Introduction to Network Layer

### 1.1. Need for Network Layer
The network layer is the third layer in the OSI model and is responsible for providing logical addressing, routing, and path determination for data packets to travel from a source host on one network to a destination host on a different network.

**Key Functions:**
*   **Logical Addressing:** Assigns unique logical addresses (e.g., IP addresses) to hosts for identification across different networks. Physical addresses (MAC addresses) are insufficient as they are locally significant.
*   **Routing:** Determines the best path for data packets to travel from source to destination across interconnected networks (internets). This involves using routing algorithms and routing tables.
*   **Packet Forwarding:** The process of moving packets from an input interface to an output interface of a router based on the destination address in the packet header and the router's forwarding table.
*   **Fragmentation and Reassembly:** If a packet is larger than the Maximum Transmission Unit (MTU) of a network segment it needs to traverse, the network layer fragments it into smaller packets. These are reassembled at the destination network layer.
*   **Inter-Networking:** Enables communication between different types of networks (e.g., Ethernet, Wi-Fi, Token Ring) by providing a common addressing scheme and routing mechanisms.
*   **Quality of Service (QoS):** Some network layer protocols can provide mechanisms to ensure a certain level of service for specific types of traffic (e.g., prioritizing voice or video).

### 1.2. Services Provided by Network Layer
The network layer provides services to the transport layer. These services can be broadly categorized as:

*   **Connectionless Service (Datagram Service):**
    *   Each packet (datagram) is treated independently.
    *   Packets may arrive out of order, be lost, or duplicated.
    *   No prior connection setup is required.
    *   Example: IP (Internet Protocol) is a connectionless protocol.
    *   **Advantages:** Simple, flexible, robust (can reroute around failures).
    *   **Disadvantages:** Unreliable; relies on higher layers (like TCP) for reliability.

*   **Connection-Oriented Service (Virtual Circuit Service):**
    *   A logical connection (virtual circuit) is established between source and destination before data transfer.
    *   All packets follow the same path.
    *   Provides guarantees like in-order delivery and reliability (though reliability is often a transport layer function enhanced by network layer support).
    *   Examples: X.25, Frame Relay, ATM (Asynchronous Transfer Mode).
    *   **Phases:**
        1.  **Setup Phase:** A virtual circuit is established. Resources might be reserved.
        2.  **Data Transfer Phase:** Packets are transmitted along the established path.
        3.  **Teardown Phase:** The virtual circuit is released.
    *   **Advantages:** Can provide QoS, ordered delivery.
    *   **Disadvantages:** More complex setup, less resilient to router failures if a path goes down.

**Comparison of Service Types:**

| Feature             | Connectionless (Datagram)     | Connection-Oriented (Virtual Circuit) |
| ------------------- | ----------------------------- | ------------------------------------- |
| **Connection Setup** | None                          | Required                              |
| **Packet Path**     | Independent per packet        | Same path for all packets             |
| **Packet Order**    | May be out of order           | Maintained (usually)                  |
| **Reliability**     | Not guaranteed by network layer | Can be higher, often with guarantees |
| **Addressing**      | Full source/destination in each packet | Virtual Circuit Identifier (VCI) in each packet after setup |
| **State in Routers**| No per-flow state (usually)   | Per-flow state required               |
| **Overhead**        | Lower setup, higher per packet (full address) | Higher setup, lower per packet (VCI) |
| **Example**         | IP                            | X.25, ATM                             |

### 1.3. Design Issues for the Network Layer
Designing the network layer involves addressing several critical challenges:

1.  **Addressing Scheme:**
    *   Must be unique globally (for public networks).
    *   Hierarchical to facilitate efficient routing (e.g., network part and host part).
    *   Sufficiently large address space to accommodate all devices.
    *   Scalability for future growth.

2.  **Routing:**
    *   **Algorithm Choice:** Selecting efficient algorithms to find optimal paths (e.g., shortest path, least cost).
    *   **Metrics:** Defining what "best path" means (e.g., hop count, delay, bandwidth, cost).
    *   **Adaptability:** Routing tables should adapt to network topology changes (dynamic routing).
    *   **Scalability:** Routing algorithms must work efficiently in large, complex networks.
    *   **Stability:** Avoid routing loops and oscillations.
    *   **Fairness and Optimality:** Balancing individual user needs with overall network efficiency.

3.  **Packet Forwarding:**
    *   Fast and efficient lookup in forwarding tables.
    *   Handling different packet formats.

4.  **Quality of Service (QoS):**
    *   Providing different levels of service for different types of traffic (e.g., delay, jitter, bandwidth guarantees).
    *   Mechanisms for traffic classification, marking, queuing, and scheduling.

5.  **Fragmentation and Reassembly:**
    *   Deciding where fragmentation occurs (source or intermediate routers).
    *   Efficiently reassembling fragments at the destination.
    *   Handling lost fragments.

6.  **Congestion Control:**
    *   Preventing the network from becoming overloaded.
    *   Mechanisms to detect and mitigate congestion (e.g., traffic shaping, random early detection).
    *   *Note: While significant congestion control happens at the transport layer (TCP), the network layer also plays a role, e.g., by signaling congestion (ICMP Source Quench).*

7.  **Security:**
    *   Protecting against attacks like IP spoofing, denial of service.
    *   Mechanisms like IPsec for authentication and encryption at the network layer.

8.  **Inter-networking:**
    *   Connecting heterogeneous networks with different protocols and technologies.
    *   Protocol translation or encapsulation.

9.  **Scalability:**
    *   The network layer protocols and architecture must scale to accommodate a growing number of networks and hosts.

## 2. Routing Algorithms
Routing algorithms determine the path that packets take from a source to a destination. They are fundamental to the operation of the network layer.

**Categories of Routing Algorithms:**

*   **Static vs. Dynamic:**
    *   **Static Routing:** Routes are manually configured by an administrator. Simple for small networks, but doesn't adapt to changes.
    *   **Dynamic Routing:** Routers exchange information to automatically update routing tables based on network conditions. More complex but adaptable.
*   **Global vs. Decentralized (Distributed):**
    *   **Global (Link State):** Each router has complete information about the network topology and link costs. Example: Dijkstra's.
    *   **Decentralized (Distance Vector):** Each router knows only about its directly connected neighbors. It makes decisions based on information from these neighbors. Example: Bellman-Ford.
*   **Intra-domain vs. Inter-domain:**
    *   **Intra-domain (Interior Gateway Protocols - IGP):** Used within an Autonomous System (AS). Examples: RIP, OSPF, EIGRP.
    *   **Inter-domain (Exterior Gateway Protocols - EGP):** Used between different Autonomous Systems. Example: BGP.

### 2.1. Least Cost Routing Algorithm (General Concept)
This is a general principle rather than a specific algorithm. The goal is to find a path between two nodes such that the sum of the costs of the links on that path is minimized. The "cost" can represent various metrics:
*   **Hop count:** Number of routers in the path (simplest metric).
*   **Delay:** Time taken for a packet to traverse the link.
*   **Bandwidth:** Capacity of the link (often inverse, so higher bandwidth means lower cost).
*   **Monetary cost:** Actual cost of using a link.
*   **Reliability:** Probability of successful transmission.

Algorithms like Dijkstra's and Bellman-Ford are specific implementations of least cost routing.

### 2.2. Dijkstra's Algorithm (Link State Algorithm)
Dijkstra's algorithm is a **global link-state algorithm** used to find the shortest paths from a single source node to all other nodes in a graph with non-negative edge weights (link costs).

**Assumptions:**
*   Non-negative link costs.
*   Each router knows the complete network topology and link costs (achieved through link-state advertisements, e.g., in OSPF).

**Algorithm Steps (Conceptual):**
1.  **Initialization:**
    *   Assign a tentative distance value to every node: zero for the initial node and infinity for all other initial nodes.
    *   Mark all nodes unvisited. Set the initial node as current.
    *   Create a set of visited nodes, initially empty.
    *   Maintain `D(v)`: current cost of the shortest path from source to node `v`.
    *   Maintain `p(v)`: predecessor node along the path from source to `v`.

2.  **Iteration:**
    *   For the current node, consider all of its unvisited neighbors and calculate their tentative distances through the current node.
    *   Compare the newly calculated tentative distance to the current assigned value and assign the smaller one. For example, if the current node `A` is marked with a distance of 6, and the edge connecting it with a neighbor `B` has length 2, then the distance to `B` through `A` will be 6 + 2 = 8. If `B` was previously marked with a distance greater than 8 then change it to 8. Otherwise, keep the current value.
    *   When all of the unvisited neighbors of the current node have been considered, mark the current node as visited and remove it from the unvisited set. A visited node will never be checked again.

3.  **Termination:**
    *   If the destination node has been marked visited (when planning a route between two specific nodes) or if the smallest tentative distance among the nodes in the unvisited set is infinity (when planning a complete shortest path tree), then stop. The algorithm has finished.

**Example:**
Consider a network graph. Dijkstra's will systematically find the shortest path from a chosen source router to all other routers.

**Characteristics:**
*   **Complexity:** O(N^2) with a simple implementation (adjacency matrix) or O(E log N) with a binary heap (adjacency list), where N is the number of nodes and E is the number of edges.
*   **Converges quickly.**
*   **Oscillations:** If link costs are based on load, oscillations can occur as traffic shifts to newly found "best" paths, making them congested and thus high-cost again.

### 2.3. Bellman-Ford Algorithm (Distance Vector Algorithm)
The Bellman-Ford algorithm is a **decentralized distance vector algorithm** used to find the shortest paths from a single source node to all other nodes in a weighted digraph. It can handle graphs with negative edge weights (though negative cycles make shortest paths ill-defined). In routing, link costs are typically non-negative.

**How it works (Conceptual for routing):**
1.  **Initialization:**
    *   Each router `x` maintains a distance vector `Dx = [Dx(y1), Dx(y2), ..., Dx(yn)]` where `Dx(y)` is the estimated cost from `x` to `y`.
    *   Initially, `Dx(x) = 0` and `Dx(y) = infinity` for `y != x`.
    *   Each router also knows the cost to its direct neighbors, `c(x,v)`.

2.  **Iteration and Information Sharing:**
    *   Periodically, each router sends its current distance vector to its direct neighbors.
    *   When a router `x` receives a distance vector `Dv` from a neighbor `v`, it updates its own distance vector using the Bellman-Ford equation:
        `Dx(y) = min_v { c(x,v) + Dv(y) }` for each destination `y`.
        This means: the cost from `x` to `y` via neighbor `v` is the cost from `x` to `v` plus the cost from `v` to `y` (as estimated by `v`). Router `x` chooses the neighbor `v` that minimizes this sum.

3.  **Convergence:**
    *   The algorithm iterates, and eventually, the distance estimates converge to the true shortest path costs.
    *   The maximum number of iterations needed is related to the diameter of the network.

**Count-to-Infinity Problem:**
*   A major issue with simple distance vector algorithms like Bellman-Ford (as used in early RIP).
*   When a link cost increases or a link fails, "good news" (a path becomes better) propagates quickly.
*   "Bad news" (a path becomes worse or fails) propagates slowly. Routers might keep sending traffic through a failed link because they haven't received updated information, leading to routing loops.
*   **Example:** If A routes to D via B, and B routes to D via C, and the link C-D fails. C tells B its cost to D is infinity. B might then think it can still reach D via A (if A hasn't updated yet), leading to A-B-A-B loops.
*   **Solutions:**
    *   **Split Horizon:** Don't advertise a route back to the neighbor from which it was learned.
    *   **Poison Reverse (Split Horizon with Poison Reverse):** Advertise the route back to the neighbor it was learned from, but with an infinite metric.
    *   **Hold-down Timers:** After a route is withdrawn, wait for a period before accepting new information about it.
    *   **Triggered Updates:** Send updates immediately upon metric changes, rather than waiting for periodic timers.

**Characteristics:**
*   **Distributed:** Each router only needs information from its neighbors.
*   **Simpler to implement locally at each router.**
*   **Slower convergence than link-state.**
*   **Susceptible to count-to-infinity and routing loops without mitigation techniques.**

### 2.4. Hierarchical Routing
As networks grow very large, flat routing schemes (where every router knows about every other router or network) become unmanageable:
*   Routing tables become enormous.
*   Routing updates consume significant bandwidth.
*   Computation of routes becomes complex and slow.

**Solution: Hierarchical Routing**
*   Divide the network into regions or **Autonomous Systems (AS)**.
*   Routers within an AS (intra-AS or intra-domain routing) know details about routes within their own AS but have a summarized view of other ASes.
*   Routers that connect different ASes (inter-AS or inter-domain routing) handle routing between ASes.
*   **Example:** The Internet is structured as a collection of ASes.
    *   **Interior Gateway Protocols (IGPs):** Used for routing within an AS (e.g., OSPF, IS-IS, RIP, EIGRP).
    *   **Exterior Gateway Protocols (EGPs):** Used for routing between ASes (e.g., BGP - Border Gateway Protocol).

**Advantages:**
*   **Scalability:** Reduces the size of routing tables and update traffic.
*   **Administrative Autonomy:** Each AS can choose its own IGP and manage its internal routing policies.

**Disadvantages:**
*   **Suboptimal Paths:** Since routers in one AS don't have detailed topology information about other ASes, the chosen inter-AS path might not be the absolute shortest path.
*   Increased complexity in managing inter-AS policies.

**How it works (Simplified):**
*   Routers are grouped into regions.
*   Each router knows how to route packets to destinations within its own region.
*   For destinations in other regions, the router sends the packet to a designated gateway or border router for that region.
*   The hierarchy can have multiple levels.

### 2.5. Broadcast Routing
Sending a packet from a source node to all other nodes in the network.

**Methods:**

1.  **Flooding (Uncontrolled):**
    *   Simplest method.
    *   When a router receives a broadcast packet, it sends it out on all links except the one it arrived on.
    *   **Problems:**
        *   Generates a vast number of duplicate packets (broadcast storm).
        *   Can lead to loops if not managed.
    *   **Control mechanisms for flooding:**
        *   **Sequence Number:** Each broadcast packet has a unique (source, sequence_number) pair. Routers discard duplicates they've already seen.
        *   **Hop Count/Time-to-Live (TTL):** Decrement a counter in the packet; discard when it reaches zero.

2.  **Spanning Tree Broadcasting:**
    *   Construct a spanning tree of the network (a subgraph that connects all nodes without cycles).
    *   The source node sends the packet along the spanning tree.
    *   Each router forwards the packet only on links that are part of the spanning tree.
    *   **Advantages:** No duplicate packets, efficient.
    *   **Disadvantages:** Requires knowledge of the spanning tree. The tree might not represent the shortest paths to all nodes.

3.  **Reverse Path Forwarding (RPF) / Reverse Path Broadcasting (RPB):**
    *   A more practical and robust method.
    *   When a router receives a broadcast packet from a source `S`, it checks if the packet arrived on the link that is on its own shortest path back to `S`.
        *   If YES: The packet is likely taking a sensible path. The router forwards the packet on all outgoing links except the one it arrived on.
        *   If NO: The packet is likely a duplicate or taking a circuitous route. The router discards it.
    *   **Advantages:** Reasonably efficient, robust, doesn't require explicit knowledge of a full spanning tree (just the shortest path to the source).
    *   Implicitly creates a sink tree for the broadcast.

### 2.6. Multicast Routing
Sending a packet from a source node to a specific group of destination nodes (the multicast group) that have expressed interest in receiving it. More efficient than sending multiple unicast packets or broadcasting to everyone.

**Key Concepts:**
*   **Multicast Group:** A set of hosts identified by a single IP multicast address (Class D address range: 224.0.0.0 to 239.255.255.255).
*   **Group Membership:** Hosts join multicast groups using protocols like IGMP (Internet Group Management Protocol). Routers use IGMP to learn which of their local networks have members of specific multicast groups.
*   **Multicast Routing Protocols:** Build distribution trees to deliver multicast packets from source(s) to group members.
    *   **Source-Based Trees:** A separate tree is built for each source sending to a group. Optimal paths from that source, but more state in routers. (e.g., DVMRP, MOSPF, PIM-DM).
    *   **Shared Trees (Core-Based Trees):** A single tree is shared by all senders for a group. Packets are sent to a "core" or "rendezvous point" (RP) and then distributed down the tree. Less state, but paths might be suboptimal. (e.g., PIM-SM, CBT).

**Common Multicast Routing Protocols:**
*   **Distance Vector Multicast Routing Protocol (DVMRP):**
    *   Uses reverse path forwarding and a distance-vector approach.
    *   Implicitly builds source-based trees.
    *   "Flood and prune" mechanism: initially floods traffic, then prunes branches that don't lead to group members.
*   **Multicast OSPF (MOSPF):**
    *   An extension to OSPF (link-state).
    *   Routers include multicast group membership information in their link-state advertisements.
    *   Each router calculates source-based trees.
*   **Protocol Independent Multicast (PIM):**
    *   Not dependent on a specific unicast routing protocol. It uses the unicast routing table to make RPF checks.
    *   Two modes:
        *   **PIM Dense Mode (PIM-DM):** Assumes group members are densely packed. Uses flood-and-prune. Suitable for smaller networks.
        *   **PIM Sparse Mode (PIM-SM):** Assumes group members are sparsely distributed. Uses shared trees (initially) and can switch to source-based trees for high-traffic sources. Requires a Rendezvous Point (RP).

## 3. IP Addresses and IP Protocol (IPv4)

### 3.1. IP Addresses
An IP address is a unique numerical label assigned to each device participating in a computer network that uses the Internet Protocol for communication.

**IPv4 Address:**
*   32-bit number.
*   Represented in dotted-decimal notation (e.g., `192.168.1.1`). Each decimal number represents 8 bits (an octet).
*   Hierarchical: Consists of a **Network ID** and a **Host ID**.
    *   **Network ID:** Identifies the network to which the device belongs. Routers use this to forward packets to the correct network.
    *   **Host ID:** Identifies a specific device (host) within that network.

**Classes of IP Addresses (Classful Addressing - largely historical but foundational):**
*   **Class A:**
    *   First bit: `0`
    *   Network ID: First 8 bits (1st octet). Host ID: Last 24 bits (3 octets).
    *   Range: `0.0.0.0` to `127.255.255.255` (Network `0` and `127` are special).
    *   Number of Networks: 2^7 - 2 = 126.
    *   Hosts per Network: 2^24 - 2 (all 0s host ID is network address, all 1s is broadcast).
    *   Use: Very large organizations.
*   **Class B:**
    *   First two bits: `10`
    *   Network ID: First 16 bits (1st and 2nd octets). Host ID: Last 16 bits (2 octets).
    *   Range: `128.0.0.0` to `191.255.255.255`.
    *   Number of Networks: 2^14.
    *   Hosts per Network: 2^16 - 2.
    *   Use: Medium to large organizations.
*   **Class C:**
    *   First three bits: `110`
    *   Network ID: First 24 bits (1st, 2nd, 3rd octets). Host ID: Last 8 bits (4th octet).
    *   Range: `192.0.0.0` to `223.255.255.255`.
    *   Number of Networks: 2^21.
    *   Hosts per Network: 2^8 - 2 = 254.
    *   Use: Small organizations.
*   **Class D:**
    *   First four bits: `1110`
    *   Range: `224.0.0.0` to `239.255.255.255`.
    *   Use: Multicast addresses. No network/host ID division.
*   **Class E:**
    *   First four bits: `1111`
    *   Range: `240.0.0.0` to `255.255.255.255`.
    *   Use: Reserved for experimental/future use.

**Special IP Addresses:**
*   **Network Address:** Host ID portion is all zeros (e.g., `192.168.1.0`). Represents the network itself.
*   **Directed Broadcast Address:** Host ID portion is all ones (e.g., `192.168.1.255`). Sends a packet to all hosts on that specific remote network.
*   **Limited Broadcast Address:** `255.255.255.255`. Sends a packet to all hosts on the local network segment.
*   **Loopback Address:** `127.0.0.1` (or any `127.x.x.x`). Used by a host to send packets to itself, for testing and local services.
*   **Private IP Addresses:** Ranges reserved for use in private networks (not routable on the public internet).
    *   Class A: `10.0.0.0` to `10.255.255.255` (10.0.0.0/8)
    *   Class B: `172.16.0.0` to `172.31.255.255` (172.16.0.0/12)
    *   Class C: `192.168.0.0` to `192.168.255.255` (192.168.0.0/16)
    *   NAT (Network Address Translation) is used to allow devices with private IPs to access the internet.

**Subnetting:**
*   Process of dividing a large network (Class A, B, or C) into smaller sub-networks (subnets).
*   "Borrows" bits from the Host ID portion to create a Subnet ID.
*   **Subnet Mask:** A 32-bit number that distinguishes the Network ID + Subnet ID portion from the Host ID portion.
    *   Binary 1s for the network and subnet parts, 0s for the host part.
    *   Example: A Class C network `200.10.20.0`. Default mask: `255.255.255.0`.
    *   If we borrow 3 bits for subnetting: New mask `11111111.11111111.11111111.11100000` which is `255.255.255.224`.
    *   This allows for 2^3 = 8 subnets. Each subnet has 2^5 - 2 = 30 hosts.
*   **Benefits:**
    *   Improved organization and management.
    *   Reduced broadcast traffic (broadcasts are confined to subnets).
    *   Enhanced security (can apply different policies to different subnets).
    *   More efficient use of IP address space.

**CIDR (Classless Inter-Domain Routing):**
*   Replaced classful addressing to slow down IPv4 address exhaustion and improve routing table efficiency.
*   Allows the network prefix to be of any length (not just 8, 16, or 24 bits).
*   Notation: `a.b.c.d/x`, where `x` is the number of bits in the network prefix.
    *   Example: `192.168.1.0/24` means the first 24 bits are the network prefix.
*   **Supernetting (Route Aggregation/Summarization):** Combining multiple smaller CIDR blocks into a larger one to reduce the number of routing table entries. For example, `200.1.0.0/24` and `200.1.1.0/24` could potentially be summarized if contiguous.

### 3.2. IPv4 Header Format
The IP header contains information necessary for routing and delivering the IP datagram.

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version|  IHL  |Type of Service|          Total Length         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Identification        |Flags|      Fragment Offset    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Time to Live |    Protocol   |        Header Checksum        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                       Source IP Address                       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Destination IP Address                     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options (if IHL > 5)                       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                            Payload                            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

*   **Version (4 bits):** IP protocol version number (e.g., 4 for IPv4).
*   **IHL (Internet Header Length) (4 bits):** Length of the IP header in 32-bit words. Minimum value is 5 (20 bytes, no options). Maximum is 15 (60 bytes).
*   **Type of Service (ToS) (8 bits):** Specifies how the datagram should be handled (e.g., priority, delay, throughput, reliability preferences). Largely replaced by Differentiated Services (DiffServ).
*   **Total Length (16 bits):** Total length of the IP datagram (header + data) in bytes. Maximum size is 65,535 bytes.
*   **Identification (16 bits):** Used to identify fragments of an original IP datagram. All fragments of the same datagram share the same identification value.
*   **Flags (3 bits):**
    *   Bit 0: Reserved (must be 0).
    *   Bit 1: DF (Don't Fragment) flag. If 1, the datagram must not be fragmented. If fragmentation is needed but DF=1, the packet is dropped and an ICMP error is sent.
    *   Bit 2: MF (More Fragments) flag. If 1, this is not the last fragment. If 0, this is the last fragment or the only fragment.
*   **Fragment Offset (13 bits):** Specifies the offset of this fragment, in 8-byte units, relative to the beginning of the original unfragmented datagram's data portion.
*   **Time to Live (TTL) (8 bits):** Specifies the maximum number of hops (routers) the datagram can pass through. Decremented by each router. If TTL reaches 0, the packet is discarded (prevents infinite loops).
*   **Protocol (8 bits):** Identifies the next-level protocol encapsulated in the IP datagram's payload (e.g., 6 for TCP, 17 for UDP, 1 for ICMP).
*   **Header Checksum (16 bits):** A checksum calculated only for the IP header. It is recomputed by each router because the TTL field (and possibly options) changes. If the checksum fails, the packet is discarded.
*   **Source IP Address (32 bits):** IP address of the original sender.
*   **Destination IP Address (32 bits):** IP address of the final recipient.
*   **Options (variable length, up to 40 bytes):** Optional field, used for features like source routing, record route, timestamping. Padded with zeros to ensure the header length is a multiple of 32 bits. Not commonly used in modern networks due to performance impact on routers.
*   **Payload/Data:** The actual data from the upper layer protocol (e.g., TCP segment, UDP datagram). Not included in the header checksum.

### 3.3. Packet Forwarding
The process by which routers direct packets towards their destination.

1.  **Routing Table Lookup:**
    *   When a router receives an IP datagram, it examines the destination IP address in the header.
    *   It consults its **routing table** (also called forwarding table or forwarding information base - FIB).
    *   The routing table contains entries that map network prefixes (or specific host addresses) to an outgoing interface and sometimes the next-hop router's IP address.
2.  **Longest Prefix Match:**
    *   If multiple entries in the routing table match the destination IP address, the router uses the **longest prefix match rule**. This means it selects the entry with the most specific network prefix (the one with the most bits matching).
    *   Example:
        *   Packet Destination: `192.168.1.75`
        *   Routing Table Entries:
            *   `192.168.1.0/24` -> Interface A
            *   `192.168.1.64/26` -> Interface B
        *   The packet will be forwarded via Interface B because `/26` is a longer (more specific) match than `/24`.
3.  **Default Route:**
    *   If no specific match is found, the router may use a **default route** (often `0.0.0.0/0`). This route directs packets to a designated "gateway of last resort," typically the router connected to the internet service provider.
4.  **TTL Decrement:** The router decrements the TTL field in the IP header. If TTL becomes 0, the packet is discarded, and an ICMP "Time Exceeded" message is usually sent back to the source.
5.  **Header Checksum Recalculation:** Since the TTL (and possibly options) changed, the router recomputes the IP header checksum.
6.  **Forwarding:** The packet is sent out the determined outgoing interface. If the outgoing link is a point-to-point link, the packet is just sent. If it's a broadcast network (like Ethernet), the router might need to resolve the next-hop IP address to a MAC address using ARP.

### 3.4. Fragmentation and Reassembly

**Why Fragmentation?**
*   Different network technologies have different **Maximum Transmission Unit (MTU)** sizes. MTU is the largest size of a PDU (Protocol Data Unit) that a layer can pass downwards. For Ethernet, the default MTU for the IP payload is 1500 bytes.
*   If an IP datagram is larger than the MTU of the network link it needs to traverse, it must be fragmented.

**Fragmentation Process (IPv4):**
*   Can be done by the source host or by an intermediate router.
*   The original IP datagram is split into multiple smaller IP datagrams (fragments).
*   Each fragment is a self-contained IP datagram with its own IP header.
*   **Header fields used for fragmentation:**
    *   **Identification:** All fragments of the same original datagram share the same Identification value.
    *   **Flags (MF bit):**
        *   The MF (More Fragments) bit is set to 1 for all fragments except the last one.
        *   The MF bit is set to 0 for the last fragment (or if the datagram was not fragmented).
    *   **Fragment Offset:** Indicates the position of this fragment's data relative to the start of the original datagram's data, measured in 8-byte units.
        *   The first fragment has an offset of 0.
        *   Subsequent fragment offsets must be multiples of 8 bytes.
    *   **Total Length:** Each fragment has its own Total Length field, reflecting its size.
    *   **DF (Don't Fragment) flag:** If set to 1, the router cannot fragment the packet. If the packet is too large for the next link, it's dropped, and an ICMP "Destination Unreachable - Fragmentation Needed and DF set" error is sent to the source.

**Reassembly Process:**
*   **Done only at the destination host.** Routers do not reassemble fragments because:
    *   Fragments might take different paths.
    *   It would add significant processing overhead and state to routers.
    *   The destination might not be reachable via all intermediate fragment paths.
*   The destination host uses the Identification field to group fragments belonging to the same original datagram.
*   The Fragment Offset and MF bit are used to reconstruct the original datagram in the correct order.
*   A timer is usually started when the first fragment arrives. If all fragments are not received within a certain time, all received fragments for that datagram are discarded (to prevent buffer exhaustion).

**Example:**
*   Original Datagram: Size 4000 bytes (20 byte header, 3980 bytes data). MTU of next network = 1500 bytes.
*   Assume header size for fragments is also 20 bytes.
*   Max data per fragment = 1500 - 20 = 1480 bytes. This must be a multiple of 8.
    *   1480 is divisible by 8.

*   **Fragment 1:**
    *   IP Header (20 bytes)
    *   Data: Bytes 0-1479 of original data (1480 bytes)
    *   Total Length: 1500
    *   Identification: Same as original
    *   Flags: MF = 1 (more fragments follow)
    *   Fragment Offset: 0 (0 / 8)
*   **Fragment 2:**
    *   IP Header (20 bytes)
    *   Data: Bytes 1480-2959 of original data (1480 bytes)
    *   Total Length: 1500
    *   Identification: Same as original
    *   Flags: MF = 1
    *   Fragment Offset: 1480 / 8 = 185
*   **Fragment 3:**
    *   IP Header (20 bytes)
    *   Data: Bytes 2960-3979 of original data (1020 bytes)
    *   Total Length: 20 + 1020 = 1040
    *   Identification: Same as original
    *   Flags: MF = 0 (last fragment)
    *   Fragment Offset: 2960 / 8 = 370

**Disadvantages of Fragmentation:**
*   **Increased Overhead:** Each fragment has its own header.
*   **Reduced Reliability:** If one fragment is lost, the entire original datagram is lost (unless the transport protocol handles retransmission of the missing part, which is complex).
*   **Performance Impact:** Fragmentation and reassembly consume processing resources.
*   **Security Concerns:** Can be exploited in some attacks (e.g., tiny fragment attack).

**Path MTU Discovery (PMTUD):**
*   A mechanism to avoid fragmentation.
*   The source host tries to determine the smallest MTU along the path to the destination.
*   It sends packets with the DF (Don't Fragment) bit set.
*   If a router needs to fragment but DF=1, it drops the packet and sends an ICMP "Fragmentation Needed" error, which includes the MTU of the problematic link.
*   The source then reduces its packet size and tries again.

### 3.5. ICMP (Internet Control Message Protocol)
ICMP is a companion protocol to IP. It is used by hosts and routers to send operational information and error reports regarding IP datagram processing. ICMP messages are encapsulated within IP datagrams.

**Key Functions:**
*   **Error Reporting:** Reports problems encountered during the processing of IP datagrams (e.g., destination unreachable, time exceeded).
*   **Network Diagnostics:** Provides tools like Ping (Echo Request/Reply) and Traceroute.

**ICMP Message Format:**
*   Encapsulated in an IP datagram (Protocol field in IP header = 1).
*   **Type (8 bits):** Identifies the ICMP message type.
*   **Code (8 bits):** Provides further details for a given message type.
*   **Checksum (16 bits):** Covers the ICMP message.
*   **Message Body (variable):** Contains additional information, often including the header and first 8 bytes of the IP datagram that caused the error.

**Common ICMP Message Types and Codes:**

*   **Type 0: Echo Reply** (Code 0)
    *   Used to reply to an Echo Request (Ping).
*   **Type 3: Destination Unreachable**
    *   Code 0: Network Unreachable
    *   Code 1: Host Unreachable
    *   Code 2: Protocol Unreachable (e.g., destination host doesn't support the protocol in the IP header)
    *   Code 3: Port Unreachable (e.g., no application listening on the destination port - often generated by UDP)
    *   Code 4: Fragmentation Needed and DF set
    *   Code 5: Source Route Failed
*   **Type 4: Source Quench** (Code 0) - Deprecated
    *   Used to indicate congestion; asks the source to reduce its sending rate.
*   **Type 5: Redirect**
    *   Code 0: Redirect Datagrams for the Network
    *   Code 1: Redirect Datagrams for the Host
    *   Sent by a router to inform a host on the same network that a more direct route exists for a specific destination.
*   **Type 8: Echo Request** (Code 0)
    *   Used by the `ping` utility to test reachability.
*   **Type 11: Time Exceeded**
    *   Code 0: TTL Expired in Transit (TTL reached 0) - Used by `traceroute`.
    *   Code 1: Fragment Reassembly Time Exceeded
*   **Type 12: Parameter Problem** (Code 0)
    *   Indicates an error in the IP header (e.g., invalid option).
*   **Type 13: Timestamp Request** (Code 0)
*   **Type 14: Timestamp Reply** (Code 0)
*   **Type 17: Address Mask Request** (Code 0)
*   **Type 18: Address Mask Reply** (Code 0)

**Important Notes on ICMP:**
*   ICMP error messages are sent back to the original **source IP address** of the datagram that caused the error.
*   ICMP error messages are **not** generated for:
    *   Other ICMP error messages (to prevent error message storms).
    *   Datagrams with a multicast destination address (unless it's the first fragment).
    *   Datagrams with a broadcast or loopback address.
    *   Fragments other than the first fragment.
*   While ICMP reports errors, it doesn't make IP reliable. Reliability is the responsibility of higher-layer protocols like TCP.

### 3.6. Comparative Study of IPv4 & IPv6

| Feature               | IPv4                                      | IPv6                                             |
| --------------------- | ----------------------------------------- | ------------------------------------------------ |
| **Address Length**    | 32 bits                                   | 128 bits                                         |
| **Address Notation**  | Dotted Decimal (e.g., 192.168.1.1)        | Hexadecimal, colon-separated (e.g., 2001:0db8:85a3:0000:0000:8a2e:0370:7334) |
| **Address Space**     | Approx. 4.3 billion (2^32) addresses     | Approx. 3.4 x 10^38 (2^128) addresses (vast)    |
| **Header Size**       | 20 bytes (without options)                | 40 bytes (fixed size, extension headers used)     |
| **Header Complexity** | More complex, variable length options     | Simpler, fixed-length base header. Options in extension headers. |
| **Checksum**          | In header; recalculated by routers        | No header checksum (done by upper layers and link layer) |
| **Fragmentation**     | Done by routers and sending host          | Done only by sending host; routers don't fragment. Path MTU Discovery is crucial. |
| **Configuration**     | Manual, DHCPv4                            | Stateless Address Autoconfiguration (SLAAC), DHCPv6 |
| **IPsec Support**     | Optional, external                        | Integrated, mandatory (though often not fully enforced) |
| **QoS Support**       | `Type of Service` field (limited use)     | `Flow Label` field for better QoS handling        |
| **Broadcast**         | Yes (broadcast addresses)                 | No broadcast; uses multicast for similar functions (e.g., link-local all nodes) |
| **Multicast**         | Yes (Class D)                             | Yes, enhanced support with larger scope fields   |
| **Anycast**           | Limited, often via BGP                    | Native support                                   |
| **Address Resolution**| ARP (Address Resolution Protocol) for MAC | NDP (Neighbor Discovery Protocol) - combines ARP, ICMP Router Discovery, ICMP Redirect |
| **Router Discovery**  | ICMP Router Discovery (optional)          | Part of NDP                                      |
| **Mobility**          | Mobile IP (complex)                       | Mobile IPv6 (more efficient)                      |
| **Security**          | Relies on IPsec implementation           | IPsec is a core part, enhancing end-to-end security |
| **Packet Size**       | Min 20 bytes, Max 65,535 bytes            | Min 1280 bytes (required link MTU), supports jumbograms (larger than 65,535 bytes via Hop-by-Hop option) |
| **Options Handling**  | In base header, processed by all routers | In extension headers, processed only by destination or designated routers |
| **Transition**        | NAT, Dual Stack, Tunneling                | Dual Stack, Tunneling (6to4, Teredo, ISATAP), Translation (NAT64/DNS64) |

**Key Advantages of IPv6:**
*   **Massively Larger Address Space:** Solves IPv4 address exhaustion.
*   **Simplified Header:** Faster processing by routers (no checksum, fixed size).
*   **Improved Support for Extensions and Options:** More flexible.
*   **Enhanced Security:** IPsec is built-in.
*   **Better QoS/Flow Labeling:** More granular traffic management.
*   **Autoconfiguration:** Easier for hosts to get addresses.
*   **No need for NAT (in many cases):** True end-to-end connectivity possible.
*   **Improved Mobility and Multicast Support.**

**Challenges of IPv6 Adoption:**
*   Slow transition due to existing IPv4 infrastructure.
*   Cost and complexity of upgrading equipment and software.
*   Need for coordination between ISPs, content providers, and end-users.
*   Reliance on transition mechanisms which can add complexity.