# Module 3: MAC Sub layer

## MAC Sub layer Introduction

The Data Link Layer is often divided into two sublayers:
1.  **Logical Link Control (LLC):** Concerned with managing communication between devices over a single link of a network. It handles error control, flow control, and framing for point-to-point links. It provides an interface to the Network Layer.
2.  **Media Access Control (MAC):** This sublayer is primarily needed when the transmission medium is shared by multiple stations (a broadcast or multi-access channel, e.g., Ethernet LAN, Wi-Fi). Its main function is to control access to the shared medium, determining which station gets to transmit next and how to handle collisions.

**Focus of this Module:** Media Access Control (MAC) sublayer.

**Why is MAC needed?**
*   In point-to-point links (only two devices), MAC is trivial as the medium is dedicated.
*   In **broadcast links** (shared medium), if multiple stations transmit simultaneously, their signals interfere with each other, causing a **collision**. The transmitted data becomes garbled and unusable.
*   The MAC sublayer implements protocols to coordinate transmissions and minimize/resolve collisions.

**The Channel Allocation Problem:** How to allocate a single broadcast channel among competing users.
*   **Static Allocation:** (e.g., FDM, TDM) - Divides channel bandwidth/time permanently. Inefficient if traffic is bursty as allocated slots might go unused.
*   **Dynamic Allocation (MAC Protocols):** Allocate channel on demand. More efficient for bursty traffic. These are the focus here.

## MAC Addressing

*   **Purpose:** To uniquely identify devices at the Data Link Layer, specifically for delivery of frames on a local network segment.
*   **MAC Address (Physical Address, Hardware Address, Ethernet Address):**
    *   Typically a **48-bit (6-byte)** address.
    *   Represented as 12 hexadecimal digits, often grouped in pairs separated by colons or hyphens (e.g., `00:1A:2B:3C:4D:5E` or `00-1A-2B-3C-4D-5E`).
    *   **Uniqueness:** Designed to be globally unique. Administered by the IEEE.
        *   The first 3 bytes (24 bits) form the **Organizationally Unique Identifier (OUI)**, assigned to a manufacturer by IEEE.
        *   The remaining 3 bytes (24 bits) are assigned by the manufacturer to be unique for each NIC (Network Interface Card) they produce.
    *   **Burned-In Address (BIA):** Usually burned into a chip on the NIC during manufacturing.
    *   Can sometimes be changed in software ("MAC spoofing"), but the hardware BIA remains.
*   **Types of MAC Addresses:**
    1.  **Unicast Address:** Identifies a single NIC. A frame sent to a unicast MAC address is intended for only one specific device.
        *   The first bit (least significant bit of the first byte, also called I/G bit - Individual/Group) is `0` for unicast.
    2.  **Multicast Address:** Identifies a group of NICs. A frame sent to a multicast MAC address is received by all devices in that multicast group.
        *   The first bit (I/G bit) is `1` for multicast.
        *   Example: IPv4 multicast IP addresses (224.0.0.0 to 239.255.255.255) map to a range of Ethernet multicast MAC addresses (e.g., `01:00:5E:xx:xx:xx`).
    3.  **Broadcast Address:** Identifies all NICs on a LAN segment. A frame sent to the broadcast MAC address is received and processed by every device on the LAN.
        *   The broadcast MAC address is `FF:FF:FF:FF:FF:FF` (all 48 bits are 1s).
        *   The I/G bit is `1`.
*   **MAC Addresses in Frames:**
    *   Ethernet frames contain both a Destination MAC Address and a Source MAC Address in their header.
    *   **Switches** use destination MAC addresses to forward frames to the correct port.
*   **Scope:** MAC addresses are used for communication *within the same local network (broadcast domain)*. They are Layer 2 addresses.
*   When a packet travels from one network to another via a router, the Layer 2 frame (and thus the MAC addresses) are stripped off and recreated for each hop.
    *   Source MAC is always the device sending the frame on that specific link.
    *   Destination MAC is the next hop device on that specific link (another host or a router interface).

## Binary Exponential Back-off (BEB) Algorithm

*   **Context:** Used primarily in contention-based MAC protocols like CSMA/CD (Ethernet) and CSMA/CA (Wi-Fi) to resolve collisions.
*   **Problem:** When a collision occurs, multiple stations have tried to transmit simultaneously. If they all try to retransmit immediately, another collision is likely.
*   **Purpose of BEB:** To make colliding stations wait a random amount of time before attempting to retransmit. The range from which this random time is chosen increases exponentially with the number of successive collisions.
*   **How it Works (Conceptual Steps, e.g., in Ethernet CSMA/CD):**
    1.  **Collision Detection:** A station detects that its transmission has collided with another.
    2.  **Jam Signal:** The station sends a brief jam signal to ensure all other stations are aware of the collision.
    3.  **Back-off:**
        *   For the `n`-th retransmission attempt (or after `n` collisions for the current frame):
            *   The station chooses a random integer `k` from the range `0 <= k < 2^c`, where `c = min(n, 10)`. (The `10` is a typical limit for the exponent, so the range doesn't grow indefinitely large. `n` is the collision count for the current frame).
            *   The station waits for `k * slot_time` before attempting to retransmit. `slot_time` is a fundamental unit of time in these protocols (related to propagation delay and frame size, typically 51.2 microseconds for 10/100 Mbps Ethernet).
        *   **Exponential Increase:** The range `[0, 2^c - 1]` doubles with each collision (0-1, 0-3, 0-7, 0-15, ... up to a maximum range, e.g., 0-1023 for Ethernet when c=10).
    4.  **Retransmission Attempt:** After waiting the random back-off time, the station senses the channel again. If idle, it attempts to retransmit.
    5.  **Give Up:** If the number of collisions for a single frame reaches a maximum limit (e.g., 16 attempts in Ethernet), the station gives up, discards the frame, and reports an error to the upper layers.
*   **Why Random?** If stations chose the same back-off time, they would collide again.
*   **Why Exponential?**
    *   Under light load (few collisions), back-off times are short, leading to quick recovery.
    *   Under heavy load (many collisions), the range of back-off times becomes larger, spreading out retransmission attempts more widely and increasing the probability of successful transmission.
*   **Fairness:** Gives all colliding stations a fair chance to retransmit.
*   **Example:**
    *   1st collision (n=1, c=1): choose `k` from {0, 1}. Wait `k * slot_time`.
    *   2nd collision (n=2, c=2): choose `k` from {0, 1, 2, 3}. Wait `k * slot_time`.
    *   ... 
    *   10th collision (n=10, c=10): choose `k` from {0, ..., 1023}. Wait `k * slot_time`.
    *   11th collision (n=11, c=10): choose `k` from {0, ..., 1023}. (Exponent capped at 10).
*   **BEB is a key component for stability and efficiency in contention-based random access schemes.**

## Distributed Random Access Schemes/Contention Schemes

These are MAC protocols where there is no central controller to decide who transmits. Stations contend for access to the shared channel. Collisions are possible and are handled by the protocol.

### 1. ALOHA Protocols (for Data Services)

Developed at the University of Hawaii for wireless packet radio networks. Historically important and foundational.

**a) Pure ALOHA:**
    *   **Principle:** Very simple - "transmit whenever you have data."
    *   **Operation:**
        1.  A station with a frame to send transmits it immediately.
        2.  The station then waits for an acknowledgment from the receiver. (Assumes a way to get ACKs, e.g., receiver sends ACK on a separate channel or the sender listens for its own transmission if on a broadcast medium like radio).
        3.  If no ACK is received within a timeout period, the station assumes a collision occurred and retransmits the frame after waiting a random back-off time (BEB can be used here).
    *   **Vulnerable Period:** A frame is vulnerable to collision if another station starts transmitting:
        *   Any time during the current frame's transmission.
        *   Any time during the *preceding* frame transmission time (if that transmission overlaps even slightly with the start of the current frame).
        *   So, the vulnerable period is `2 * T_fr` (where `T_fr` is the time to transmit one frame).
        ![Pure ALOHA Vulnerable Period](https://i.imgur.com/example_pure_aloha.png) *(Illustrative - no actual image here)*
    *   **Throughput (S):** The maximum achievable channel utilization.
        *   `S = G * e^(-2G)` where `G` is the offered load (average number of frames attempted per frame transmission time).
        *   **Maximum throughput `S_max` is `1 / (2e)` which is approximately `0.184` or 18.4%.**
        *   This means at best, only 18.4% of the channel's capacity is used for successful transmissions; the rest is lost to collisions or idle time.
    *   **Advantages:** Simple to implement.
    *   **Disadvantages:** Very low maximum throughput due to high probability of collisions.

**b) Slotted ALOHA:**
    *   **Principle:** An improvement over Pure ALOHA by discretizing time into slots.
    *   **Operation:**
        1.  Time is divided into equal-sized slots. The slot duration is equal to the frame transmission time (`T_fr`).
        2.  Stations are synchronized and can only begin transmitting at the start of a time slot.
        3.  If a station has a frame, it waits for the beginning of the next slot and then transmits.
        4.  If two or more stations transmit in the same slot, a collision occurs.
        5.  ACKs and retransmissions (with random back-off, choosing a random number of future slots to wait) are handled similarly to Pure ALOHA.
    *   **Vulnerable Period:** Reduced to `1 * T_fr`. A collision only occurs if two or more stations decide to transmit in the *exact same slot*.
        ![Slotted ALOHA Vulnerable Period](https://i.imgur.com/example_slotted_aloha.png) *(Illustrative - no actual image here)*
    *   **Throughput (S):**
        *   `S = G * e^(-G)`
        *   **Maximum throughput `S_max` is `1 / e` which is approximately `0.368` or 36.8%.**
        *   This is double the throughput of Pure ALOHA.
    *   **Advantages:** Higher throughput than Pure ALOHA.
    *   **Disadvantages:** Requires time synchronization among all stations, which can add complexity.

### 2. Carrier Sense Multiple Access (CSMA) Protocols (for Local-Area Networks)

CSMA protocols improve upon ALOHA by having stations **listen to the channel (carrier sense)** before transmitting. This helps to avoid transmitting if the channel is already busy, reducing collisions.

**Key Idea: "Listen Before Talk" (LBT)**

**Propagation Delay (`T_p`):** The time it takes for a signal to travel from one end of the medium to the other. This is a critical factor in CSMA protocols because a station might sense the channel as idle, start transmitting, but another distant station might have already started transmitting but its signal hasn't reached the first station yet.

**Persistence Algorithms (What to do if the channel is busy):**

*   **1-Persistent CSMA:**
    1.  Sense the channel.
    2.  If idle, transmit immediately.
    3.  If busy, continuously sense the channel until it becomes idle, then transmit immediately (with probability 1).
    *   **Problem:** If multiple stations are waiting for a busy channel to become idle, they will all transmit as soon as it does, leading to a high probability of collision.
    *   Greedy approach.

*   **Non-Persistent CSMA:**
    1.  Sense the channel.
    2.  If idle, transmit immediately.
    3.  If busy, wait a **random back-off time** and then go back to step 1 (sense again).
    *   **Advantage:** Reduces collision probability compared to 1-persistent because waiting stations don't all jump in at once.
    *   **Disadvantage:** Can lead to longer delays if the back-off time is large and the channel becomes idle during that wait.

*   **p-Persistent CSMA:** (Used with slotted channels)
    1.  Sense the channel.
    2.  If idle, transmit with probability `p`. With probability `(1-p)`, defer to the next time slot and repeat step 1.
    3.  If busy, continuously sense until idle. Then, proceed with step 2.
    *   This is a compromise between 1-persistent and non-persistent.
    *   The value of `p` is chosen to balance efficiency and collision avoidance.

**CSMA with Collision Detection (CSMA/CD):**

*   **Principle:** Extends CSMA by adding **collision detection**. Stations not only listen before talk, but also **listen while talk**.
*   **Operation (e.g., Ethernet):**
    1.  A station with a frame to send senses the channel (using one of the persistence methods, e.g., 1-persistent).
    2.  If the channel is idle, it starts transmitting the frame.
    3.  **While transmitting, the station continuously monitors the channel for collisions.** A collision can be detected if the signal level on the channel is higher than what its own transmission would produce, or if the received signal differs from the transmitted signal.
    4.  **If no collision is detected** for the duration it takes the signal to propagate to the furthest end of the cable and back (the `slot time` or `2 * T_p`), the station assumes successful transmission (once the entire frame is sent).
    5.  **If a collision is detected:**
        a.  The transmitting station immediately aborts its transmission.
        b.  It sends a brief **jam signal** (e.g., 32-48 bits) to ensure all other stations (including other colliding stations) recognize the collision and discard the garbled frame.
        c.  It then invokes the **Binary Exponential Back-off (BEB)** algorithm to choose a random waiting time.
        d.  After the back-off period, it returns to step 1 (sense the channel again).
*   **Slot Time (`2 * T_p`):** A critical parameter. For CSMA/CD to work, a station must be able to detect a collision *before* it finishes transmitting its smallest possible frame. This ensures that if a collision occurs, the station knows it happened to *its* frame.
    *   Minimum frame size is related to slot time and data rate. `MinFrameSize = SlotTime * DataRate`.
    *   If a frame is shorter than this, a collision might occur near the far end, and the transmitting station might finish sending its short frame before the collision signal travels back, mistakenly thinking the transmission was successful.
*   **Throughput:** Significantly better than ALOHA and basic CSMA, especially under moderate load. Performance degrades under very heavy load due to increased collisions.
*   **Usage:** The basis for classic **Ethernet** (IEEE 802.3) over shared coaxial cable or hubs (half-duplex mode).
*   **Not used in full-duplex switched Ethernet** because dedicated links per station mean no collisions.

**CSMA with Collision Avoidance (CSMA/CA):**

*   **Principle:** Used primarily in **wireless networks (e.g., IEEE 802.11 Wi-Fi)** where true collision detection is difficult or impossible.
    *   **Why CD is hard in wireless:**
        1.  **Hidden Terminal Problem:** Station A can hear station B, station C can hear B, but A and C cannot hear each other due to range or obstacles. If A and C transmit to B simultaneously, B experiences a collision, but A and C don't detect it themselves.
        2.  **Exposed Terminal Problem:** Station B is transmitting to A. Station C wants to transmit to D. C hears B's transmission and defers, even though its transmission to D would not interfere with B's reception at A.
        3.  **Fading/Signal Strength:** A station's own strong transmitted signal can overwhelm the weak signal of a colliding station, making it hard to detect the collision by listening while transmitting.
*   **Focus on Collision Avoidance rather than Detection & Recovery:**
    *   **Interframe Spacing (IFS):** Stations must wait a specific period of time (IFS) after the channel becomes idle before trying to transmit. Different IFS types prioritize different traffic (e.g., SIFS - Short IFS for ACKs, DIFS - Distributed IFS for regular data).
    *   **Contention Window (Back-off):** If the channel is idle after DIFS, stations with data to send choose a random back-off time from a **contention window** (similar to BEB, but often used before initial transmission too, not just after collisions). The station with the smallest random back-off time transmits first.
    *   **Acknowledgments (ACKs):** Since CD is not reliable, 802.11 uses explicit ACKs from the receiver for each unicast data frame. If the sender doesn't receive an ACK, it assumes a collision or loss and retransmits (after another back-off).
*   **Optional RTS/CTS Mechanism (Request to Send / Clear to Send):**
    *   Helps mitigate the hidden terminal problem, especially for larger data frames.
    1.  Station A (sender) wants to send data to B.
    2.  A sends a short **RTS frame** to B, indicating the duration of the upcoming data transmission.
    3.  If B receives RTS and is ready, it replies with a short **CTS frame**, also indicating the duration.
    4.  All stations that hear the RTS or CTS (even if they can't hear both A and B) learn about the upcoming transmission and its duration. They set a **Network Allocation Vector (NAV)**, which is a timer indicating how long they must defer access to the medium.
    5.  A then transmits its data frame to B.
    6.  B sends an ACK.
    *   **Overhead:** RTS/CTS adds overhead, so it's typically used only for frames larger than a certain threshold.
*   **NAV (Network Allocation Vector):** Each station maintains a NAV. When a station hears an RTS or CTS frame containing a duration, it sets its NAV for that duration. A station can only attempt to transmit if its NAV is zero.
*   **Usage:** IEEE 802.11 (Wi-Fi) and other wireless LAN standards.

**Comparison: CSMA/CD vs. CSMA/CA**

| Feature          | CSMA/CD (e.g., Classic Ethernet)              | CSMA/CA (e.g., Wi-Fi)                         |
|------------------|-----------------------------------------------|-----------------------------------------------|
| **Stands For**   | Collision Detection                           | Collision Avoidance                           |
| **Environment**  | Wired LANs (shared medium)                    | Wireless LANs                                 |
| **Collision Handling** | Detects collisions, aborts, jams, back-offs | Tries to avoid; relies on ACKs for recovery   |
| **When it Acts** | After a collision occurs                      | Before a collision is likely (proactive)      |
| **Efficiency**   | Generally higher if collisions are infrequent | Less efficient due to overhead (IFS, back-off before send, RTS/CTS) |
| **Complexity**   | Simpler in some aspects (no RTS/CTS/NAV)    | More complex due to avoidance mechanisms      |
| **ACKs**         | Not inherently part of CSMA/CD itself for data | Explicit ACKs for unicast data frames are crucial |
| **Key Mechanisms**| Listen while talk, Jam signal, BEB            | IFS, Contention Window (BEB-like), RTS/CTS (optional), NAV, ACKs |

## Collision-Free Protocols

These protocols aim to ensure that collisions *never* occur. They typically involve more coordination or explicit turn-taking than contention schemes.

**Motivation:** Contention protocols (ALOHA, CSMA) have performance that degrades under high load due to increasing collisions. Collision-free protocols can provide deterministic or more predictable access times.

1.  **Basic Bit-Map Protocol (Reservation Protocol):**
    *   **Operation:**
        1.  The contention period consists of `N` slots, where `N` is the number of stations.
        2.  If station `i` has a frame to send, it transmits a `1` bit in the `i`-th slot of the contention period. If it has no frame, it transmits nothing (or a `0`).
        3.  After all `N` slots have passed, every station knows which stations wish to transmit (those that sent a `1`).
        4.  Transmissions then occur in a pre-defined order (e.g., station 0, then station 1, etc.) among those that reserved a slot.
    *   **Overhead:** `N` bits for reservation in each cycle, even if few stations want to send.
    *   **Efficiency:** Good if many stations have data (high load). Poor if few stations have data (low load) due to fixed reservation slot overhead.
    *   **Example:** If N=8 stations, and stations 0, 2, 5 want to send:
        Reservation: `1 0 1 0 0 1 0 0`
        Data Tx: Station 0 sends, then Station 2 sends, then Station 5 sends.

2.  **Binary Countdown (Implicit Reservation by Address):**
    *   **Operation:**
        1.  When a station wants to transmit, it broadcasts its address (or some unique identifier) as a binary string, MSB first.
        2.  All stations broadcast their addresses simultaneously, bit by bit.
        3.  **Rule:** If a station sees that a higher-order bit position that was `0` in its own address has been overwritten by a `1` from another station (assuming wired OR logic on the channel, or similar), it gives up (becomes silent).
        4.  Only the station with the highest address will be able to broadcast its entire address without being forced to give up.
        5.  This station then transmits its data frame.
        6.  The process repeats for the next contention.
    *   **Channel Logic:** Assumes that if multiple stations transmit bits simultaneously, the result on the channel is the logical OR of the bits (e.g., if one sends 1 and another 0, the channel sees 1). Or, it requires a mechanism for stations to detect if their bit was overridden.
    *   **Example:** Stations A=1011, B=1001, C=1100
        *   Slot 1 (MSB): A sends 1, B sends 1, C sends 1. All continue.
        *   Slot 2: A sends 0, B sends 0, C sends 1. Channel sees 1. A and B detect their '0' was overridden by C's '1'. A and B give up.
        *   Slot 3: Only C continues. C sends 0.
        *   Slot 4: Only C continues. C sends 0.
        *   C wins and transmits its frame.
    *   **Advantages:** Efficient in terms of reservation overhead (uses station addresses directly). Prioritizes higher-addressed stations implicitly.
    *   **Disadvantages:** Implicitly prioritizes stations with higher binary addresses, which might not be fair. Requires bit-level synchronization.

3.  **Adaptive Tree Walk Protocol (Hierarchical Polling/Probing):**
    *   **Concept:** Divides stations into a logical tree structure. The protocol probes parts of the tree to find stations ready to transmit.
    *   **Operation (Simplified):**
        1.  Imagine all stations are leaves of a binary tree.
        2.  The central controller (or a distributed algorithm) starts by polling (or enabling) the root of the tree.
        3.  If there's a collision (meaning multiple stations under that root node want to transmit), the controller recursively polls the children of that node.
            *   For example, poll left child. If collision, recurse on left child. If no collision but a transmission, allow it. If no collision and no transmission, poll right child.
        4.  This continues until a single ready station is found at a leaf node, which then transmits.
        5.  The search can be optimized (e.g., if a poll of a subtree results in no response, that entire subtree can be skipped for a while).
    *   **Adaptive:** The depth of the search adapts to the number of ready stations.
        *   If only one station is ready, it can be found quickly.
        *   If many stations are ready, the search goes deeper.
    *   **Advantages:** Performs well under a wide range of loads. Can locate all ready stations efficiently.
    *   **Disadvantages:** Can be complex to implement. Performance depends on the efficiency of the tree search.
    *   **Variations:** Different ways to traverse the tree (depth-first, breadth-first) and different probing mechanisms.

**Comparison:**
*   **Bit-Map:** Simple, fixed overhead. Good at high load, bad at low.
*   **Binary Countdown:** Efficient reservation, implicit prioritization. Requires specific channel properties.
*   **Adaptive Tree Walk:** More complex, adaptive to load. Good for finding all ready stations.

These protocols are less common in general-purpose LANs (which favor CSMA-based approaches due to simplicity and good performance at low to moderate loads) but are used in specific contexts or as theoretical bases for other protocols (e.g., some satellite access schemes, older LAN standards like Token Bus which had a logical ring but physical bus and used a form of token passing which is a controlled access, collision-free method).

## Performance Measuring Metrics for MAC Protocols

When evaluating and comparing MAC protocols, several metrics are important:

1.  **Throughput (S):**
    *   The rate at which useful data is successfully transmitted over the channel.
    *   Often expressed as a fraction of the channel's raw capacity (normalized throughput, ranging from 0 to 1) or in bits/second.
    *   `S_max` is the maximum achievable throughput.
    *   Affected by collisions, protocol overhead, idle time.

2.  **Delay (D):**
    *   The average time taken for a frame to be successfully transmitted from source to destination, including:
        *   **Queueing Delay:** Time spent waiting in the sender's buffer before transmission attempt.
        *   **Access Delay:** Time spent contending for the channel (e.g., sensing, back-off, waiting for token/poll).
        *   **Transmission Delay (`T_fr`):** `FrameSize / DataRate`.
        *   **Propagation Delay (`T_p`):** Time for bits to travel across the medium.
        *   **Retransmission Delay:** Additional delays due to collisions and subsequent retransmissions.
    *   Often analyzed as load `G` increases.

3.  **Channel Utilization:**
    *   Similar to throughput, it's the fraction of time the channel is used for successful data transmission.
    *   `Utilization = SuccessfulTransmissionTime / TotalTime`.

4.  **Fairness:**
    *   How equitably the channel access is distributed among competing stations.
    *   A fair protocol should provide similar performance (throughput, delay) to all stations under similar conditions, or implement desired prioritization.
    *   Metrics: Jain's Fairness Index can be used.

5.  **Stability:**
    *   The ability of the protocol to maintain consistent performance as load increases, without collapsing (e.g., throughput dropping to zero at high loads).
    *   ALOHA protocols can be unstable; CSMA/CD with BEB is designed to be stable.

6.  **Scalability:**
    *   How well the protocol performs as the number of stations (`N`) or the geographic area (affecting `T_p`) increases.

7.  **Complexity:**
    *   Implementation complexity of the protocol (hardware and software).
    *   Simpler protocols are often preferred if they meet performance requirements.

8.  **Robustness/Reliability:**
    *   How well the protocol handles errors, node failures, or unexpected conditions.

**Relationship between Throughput and Load (G):**
*   For contention protocols, as offered load `G` (attempted transmissions) increases:
    *   Throughput `S` initially increases.
    *   Reaches a maximum `S_max` at an optimal load.
    *   Then, `S` decreases as `G` further increases, due to excessive collisions (channel saturation).
    ![Throughput vs Load Curve](https://i.imgur.com/example_S_vs_G.png) *(Illustrative - no actual image here)*

## IEEE Standards 802 series & their Variant

The IEEE (Institute of Electrical and Electronics Engineers) 802 committee develops and maintains networking standards, particularly for LANs and MANs. The Data Link Layer in IEEE 802 standards is split into LLC (Logical Link Control) and MAC sublayers.

*   **IEEE 802.1 (Higher Layer LAN Protocols Working Group):**
    *   Focuses on LAN/MAN bridging, virtual LANs (VLANs), network management, and overall architecture.
    *   **802.1Q:** VLAN Tagging. Allows multiple logical LANs to operate on a single physical LAN infrastructure. Frames are tagged with a VLAN ID.
    *   **802.1X:** Port-Based Network Access Control (PNAC). Provides authentication for devices connecting to a LAN port (wired or wireless).
    *   **802.1D:** Spanning Tree Protocol (STP). Prevents bridge loops in Ethernet networks by creating a tree topology of active links. (Later superseded by RSTP, MSTP).
    *   **802.1AB:** Link Layer Discovery Protocol (LLDP). Allows network devices to advertise their identity, capabilities, and neighbors on a LAN.
    *   **802.1aq:** Shortest Path Bridging (SPB). An advanced alternative to STP for larger networks.

*   **IEEE 802.2 (Logical Link Control - LLC):** *(Historically defined LLC, now largely integrated or less distinct in modern usage)*
    *   Defined a single LLC protocol that could be used with various MAC protocols (802.3, 802.5, etc.).
    *   Provided services to the Network Layer (Type 1: unacknowledged connectionless, Type 2: connection-oriented, Type 3: acknowledged connectionless).
    *   Modern Ethernet typically uses an EtherType field to directly identify the Network Layer protocol, often bypassing a distinct 802.2 LLC header for IP traffic.

*   **IEEE 802.3 (Ethernet Working Group):**
    *   Defines standards for **Ethernet** LANs, including various physical layer specifications and the CSMA/CD MAC protocol (for half-duplex shared media).
    *   **Variants (Physical Layer & Speed):**
        *   **Original Ethernet (10 Mbps):**
            *   `10BASE5` (Thicknet): Thick coaxial cable, bus topology.
            *   `10BASE2` (Thinnet/Cheapernet): Thin coaxial cable, bus topology.
            *   `10BASE-T`: Twisted-pair copper (Cat3 or better), star topology with hubs (later switches).
            *   `10BASE-F`: Fiber optic.
        *   **Fast Ethernet (100 Mbps):**
            *   `100BASE-TX`: Two pairs of Cat5 UTP cable.
            *   `100BASE-T4`: Four pairs of Cat3 UTP cable (less common).
            *   `100BASE-FX`: Two strands of multimode fiber.
        *   **Gigabit Ethernet (1 Gbps):**
            *   `1000BASE-T`: Four pairs of Cat5e/Cat6 UTP cable.
            *   `1000BASE-SX`: Short-wavelength laser over multimode fiber.
            *   `1000BASE-LX`: Long-wavelength laser over single-mode or multimode fiber.
            *   `1000BASE-CX`: Short copper twinax cables (for data centers).
        *   **10 Gigabit Ethernet (10 Gbps):**
            *   `10GBASE-T`: Cat6a/Cat7 UTP cable.
            *   `10GBASE-SR/LR/ER/ZR`: Various fiber optic standards for different distances (Short Reach, Long Reach, Extended Reach, Ze best Reach).
        *   **Higher Speeds:** 25 Gbps, 40 Gbps, 50 Gbps, 100 Gbps, 200 Gbps, 400 Gbps, and beyond, primarily using fiber optics, but also some copper (e.g., direct attach copper - DAC).
    *   **Key aspects:** Frame format (Preamble, SFD, MAC addresses, EtherType/Length, Data, FCS), CSMA/CD (for shared media), full-duplex operation (for switched links), auto-negotiation.

*   **IEEE 802.4 (Token Bus Working Group):** *(Obsolete)*
    *   Defined a MAC protocol using token passing on a physical bus topology.
    *   A logical ring was formed among stations, and a token circulated. Only the station holding the token could transmit.
    *   Used in industrial automation (e.g., MAP - Manufacturing Automation Protocol).

*   **IEEE 802.5 (Token Ring Working Group):** *(Largely obsolete)*
    *   Defined a MAC protocol using token passing on a physical ring topology (often star-wired ring to a MAU - Multistation Access Unit).
    *   A token circulates, and a station seizes the token to transmit.
    *   Offered deterministic access, good for applications requiring predictable delay.
    *   Competed with Ethernet, but Ethernet became dominant due to cost and simplicity.

*   **IEEE 802.6 (Metropolitan Area Network - MAN Working Group):** *(Obsolete, functionality absorbed by others)*
    *   Defined Distributed Queue Dual Bus (DQDB) for MANs.

*   **IEEE 802.11 (Wireless LAN - WLAN Working Group):**
    *   Defines standards for **Wi-Fi**.
    *   MAC Protocol: Primarily **CSMA/CA** (with mechanisms like IFS, contention window, RTS/CTS, NAV, ACKs).
    *   **Variants (Physical Layer & Amendments, a few key ones):**
        *   `802.11` (Legacy, 1997): 1-2 Mbps, 2.4 GHz (FHSS or DSSS).
        *   `802.11b` (1999): Up to 11 Mbps, 2.4 GHz (DSSS, HR-DSSS). Widespread adoption.
        *   `802.11a` (1999): Up to 54 Mbps, 5 GHz (OFDM). Less prone to interference than 2.4 GHz but shorter range.
        *   `802.11g` (2003): Up to 54 Mbps, 2.4 GHz (OFDM, compatible with 802.11b).
        *   `802.11n` (Wi-Fi 4, 2009): Higher throughput (e.g., 150-600 Mbps) using MIMO (Multiple-Input Multiple-Output), channel bonding. Operates in 2.4 GHz and/or 5 GHz.
        *   `802.11ac` (Wi-Fi 5, 2013): Even higher throughput (e.g., 433 Mbps to several Gbps) using wider channels (up to 160 MHz), more MIMO streams, MU-MIMO (Multi-User MIMO). Operates primarily in 5 GHz.
        *   `802.11ax` (Wi-Fi 6/6E, 2019/2020): Focuses on efficiency in dense environments, higher capacity, better performance. Uses OFDMA, MU-MIMO (uplink & downlink), BSS Coloring, Target Wake Time (TWT). Operates in 2.4 GHz, 5 GHz, and 6 GHz (Wi-Fi 6E).
        *   `802.11be` (Wi-Fi 7, upcoming): Aims for Extremely High Throughput (EHT), lower latency. Features like 320 MHz channels, 4K QAM, Multi-Link Operation (MLO).
    *   **Other 802.11 amendments cover:** Security (802.11i - WPA2/WPA3), QoS (802.11e), Mesh (802.11s), Fast Roaming (802.11r), etc.

*   **IEEE 802.15 (Wireless Personal Area Network - WPAN Working Group):**
    *   **802.15.1 (Bluetooth):** Short-range wireless for PANs (e.g., connecting headsets, keyboards, data transfer between devices). Uses frequency hopping.
    *   **802.15.3 (High-Rate WPAN):** For high-speed multimedia (e.g., UWB - Ultra-Wideband).
    *   **802.15.4 (Low-Rate WPAN - LR-WPAN):** For low-power, low-data-rate devices (e.g., Zigbee, WirelessHART, Thread). Basis for many IoT applications.

*   **IEEE 802.16 (Broadband Wireless Access Working Group - WiMAX):**
    *   Defines standards for **WiMAX** (Worldwide Interoperability for Microwave Access).
    *   Aimed for wireless broadband access over longer distances (MANs).
    *   Offered fixed and mobile broadband wireless access.
    *   Less prevalent now compared to LTE/5G cellular technologies for wide area broadband.

*   **IEEE 802.22 (Wireless Regional Area Network - WRAN):**
    *   Aims to use unused TV broadcast channels (TV white spaces) for rural broadband wireless access.
    *   Cognitive radio capabilities to avoid interfering with licensed TV broadcasts.

This provides an overview of the MAC sublayer concepts and the prominent IEEE 802 standards. Each standard (like 802.3 Ethernet or 802.11 Wi-Fi) is a large topic in itself with many details. 