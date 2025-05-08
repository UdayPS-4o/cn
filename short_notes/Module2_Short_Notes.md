# Module 2: Short Notes - Data Link Layer

## Key Concepts & Exam Quick Call

### 1. DLL: Need & Services
*   **Need:** Reliable node-to-node (hop-to-hop) data transfer, manages physical layer issues, interface to Network Layer, shared access medium control.
*   **Services Provided (Remember "Framing Physical Flow Errs Access Sync Seq"):**
    1.  **Framing:** Divides bit stream into frames (Header + Payload + Trailer). Ethernet: Preamble, SFD, MAC Addrs, Type/Len, Data, FCS (CRC).
        *   **Methods:**
            *   **Character Count:** Header count (unreliable if corrupted).
            *   **Character/Byte Stuffing:** Flag bytes (e.g., DLE STX, DLE ETX); stuff DLE in data if it appears. (PPP: `0x7E` flag, `0x7D` escape).
            *   **Bit Stuffing:** Flag pattern (e.g., `01111110`); stuff `0` after five 1s in data. (HDLC).
            *   **Physical Layer Coding Violations:** Non-data symbols for boundaries.
    2.  **Physical Addressing (MAC Addressing):** L2 hardware address for local delivery (e.g., Ethernet MAC).
    3.  **Flow Control:** Prevents fast sender from swamping slow receiver.
        *   **Stop-and-Wait:** Send 1 frame, wait for ACK. Inefficient.
        *   **Sliding Window:** Send multiple frames (window) before ACK. More efficient.
    4.  **Error Control:** Detects/corrects errors.
        *   **Error Detection:**
            *   **Parity Check:** Single bit for even/odd 1s. Detects single-bit errors.
            *   **Checksum:** Sum of data segments. Used in IP/TCP/UDP.
            *   **CRC (Cyclic Redundancy Check):** Polynomial division. Powerful, common (Ethernet FCS).
        *   **Error Correction (FEC - Forward Error Correction):**
            *   **Hamming Codes:** Correct single-bit errors, detect double-bit.
        *   **ARQ (Automatic Repeat reQuest):** Error detection + retransmission (Stop-and-Wait ARQ, Go-Back-N ARQ, Selective Repeat ARQ).
    5.  **Access Control (MAC sublayer):** Manages shared medium access (e.g., CSMA/CD).
    6.  **Synchronization:** Frame boundary detection.
    7.  **Sequencing:** Order frames (in connection-oriented DLL service).
*   **DLL Service Types:**
    *   **Unacknowledged Connectionless:** No ACKs, no connection (e.g., Ethernet). Fast, relies on upper layers for reliability.
    *   **Acknowledged Connectionless:** ACKs per frame, no connection (e.g., Wi-Fi 802.11).
    *   **Acknowledged Connection-Oriented:** Connection setup, numbered/ACKed frames. Reliable (e.g., some WAN links).

### 2. Data Link Protocols (Flow & Error Control)

*   **Elementary Protocols (Conceptual):**
    *   Utopian Simplex: Ideal, no control.
    *   Stop-and-Wait Simplex: Basic flow control.
    *   Stop-and-Wait ARQ: Flow + Error control (1-bit sliding window). Seq # (0,1) for frames/ACKs, timeouts.

*   **Sliding Window ARQ Protocols:** Sender can send `SWS` frames, Receiver expects `RWS` frames.
    *   **1. One-Bit Sliding Window:** = Stop-and-Wait ARQ. `SWS=1, RWS=1`.
    *   **2. Go-Back-N ARQ (GBN):**
        *   `SWS <= 2^m - 1`, `RWS = 1` (`m` = seq # bits).
        *   **Sender:** Sends up to SWS frames. If frame `i` lost/error (timeout/NAK), retransmits frame `i` AND all subsequent frames already sent (F_i, F_i+1, ...).
        *   **Receiver:** Accepts only in-order frames. Discards out-of-order frames. Sends cumulative ACKs (ACK `n` = got all up to `n-1`, expecting `n`).
        *   **Pros:** Simpler receiver. **Cons:** Wastes bandwidth by re-sending correct frames.
    *   **3. Selective Repeat ARQ (SR):**
        *   `SWS = RWS <= 2^(m-1)`.
        *   **Sender:** Sends up to SWS frames. Individual timer per frame. If frame `i` lost/error (timeout/NAK), retransmits *only* frame `i`.
        *   **Receiver:** Accepts & buffers out-of-order frames (if within RWS). Sends SACKs (Selective ACKs) for correctly received frames. Delivers to Network Layer in order.
        *   **Pros:** Most efficient (only bad frames resent). **Cons:** Complex receiver (buffering, reordering), sender timers.
    *   **Window Size `W` for efficiency:** `W >= 1 + a` where `a = (2 * Propagation_Delay) / Frame_Transmission_Time`.
    *   **Hybrid ARQ (HARQ):** Combines FEC + ARQ. Improves performance on noisy channels (wireless). Chase Combining, Incremental Redundancy.

### 3. Protocol Verification
*   **Goal:** Ensure protocol correctness (no deadlocks, livelocks, etc.).
*   **Techniques:**
    *   **Finite State Machines (FSM):** Model entities as states, transitions, events, actions. Analyze reachability (all possible global states).
        *   Detects: Deadlocks, unspecified receptions.
        *   Challenge: State space explosion.
    *   **Petri Nets:** Bipartite graph (places, transitions, tokens). Good for concurrency/synchronization.
        *   Analyze: Reachability graph, invariants, boundedness, liveness.
        *   Types: Basic, Colored (CPN), Timed.

### 4. ARP / RARP
*   **Address Resolution Protocol (ARP):** (L3 IP to L2 MAC on *local* network)
    *   **Problem:** Host A knows `IP_B` (target), needs `MAC_B` for L2 frame.
    *   **How:**
        1.  A checks **ARP Cache** for `IP_B` -> `MAC_B` mapping.
        2.  If not found: A broadcasts **ARP Request** (`Who has IP_B? Tell MAC_A/IP_A`). Dest MAC = `FF:FF:FF:FF:FF:FF`.
        3.  Host B (owner of `IP_B`) replies with unicast **ARP Reply** (`IP_B is at MAC_B`). Dest MAC = `MAC_A`.
        4.  A caches `IP_B` -> `MAC_B`.
    *   **Gratuitous ARP:** Host sends ARP for its *own* IP. Uses: Duplicate IP detection, cache update.
    *   **ARP is local:** Routers don't forward ARP. To send to remote net, host ARPs for default gateway's MAC.
*   **Reverse ARP (RARP):** (L2 MAC to L3 IP - *Obsolete*)
    *   **Problem:** Diskless station knows its MAC, needs its IP at boot.
    *   **How:** Client broadcasts RARP Request. RARP server replies with client's IP.
    *   **Limitations:** Needs RARP server per LAN, only gives IP (no mask/gateway), manual config.
    *   **Superseded by:** BOOTP, **DHCP** (Dynamic Host Configuration Protocol).

--- 
**Quick Exam Tips for Module 2:**
*   Clearly explain Framing: why it's needed and different methods (bit/byte stuffing crucial).
*   Differentiate Flow Control & Error Control.
*   Master Stop-and-Wait ARQ, Go-Back-N ARQ, Selective Repeat ARQ: know window sizes, sender/receiver actions, pros/cons for each.
*   Error Detection: Parity, Checksum, and especially CRC (how it works conceptually).
*   ARP: Purpose, full request/reply process, ARP cache. Why it's local.
*   RARP: Purpose, process, and why it's obsolete (replaced by DHCP).
*   Protocol Verification: Basic idea of FSM and Petri Net models. 