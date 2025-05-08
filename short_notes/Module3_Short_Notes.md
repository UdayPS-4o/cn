# Module 3: Short Notes - MAC Sub layer

## Key Concepts & Exam Quick Call

### 1. MAC Sublayer Basics
*   **DLL Split:** LLC (Logical Link Control) & MAC (Media Access Control).
*   **MAC Purpose:** Controls access to shared broadcast medium to prevent/manage collisions.
*   **Channel Allocation:** Static (FDM/TDM - inefficient for bursty) vs. Dynamic (MAC protocols).

### 2. MAC Addressing
*   **MAC Address (Physical/Hardware Address):** 48-bit (6-byte) unique ID (e.g., `00:1A:2B:3C:4D:5E`).
    *   **OUI (Organizationally Unique Identifier):** First 3 bytes (IEEE assigned to vendor).
    *   **Vendor Assigned:** Last 3 bytes (unique per NIC).
*   **Types:**
    *   **Unicast:** Single NIC (I/G bit = 0).
    *   **Multicast:** Group of NICs (I/G bit = 1; e.g., `01:00:5E:xx:xx:xx` for IPv4 multicast).
    *   **Broadcast:** All NICs on LAN (`FF:FF:FF:FF:FF:FF`).
*   **Scope:** Local network (broadcast domain) only. Re-written by routers at each hop.

### 3. Binary Exponential Back-off (BEB) Algorithm
*   **Use:** Resolves collisions in CSMA/CD (Ethernet) & CSMA/CA (Wi-Fi).
*   **How:** After `n`-th collision, choose random `k` from `0` to `2^c - 1` (where `c = min(n, 10)`). Wait `k * slot_time`.
*   **Why Random & Exponential:** Avoids repeated collisions, adapts to load (short waits for light load, longer for heavy).
*   **Give Up:** After max attempts (e.g., 16 for Ethernet).

### 4. Distributed Random Access/Contention Schemes
(Stations contend for channel; collisions possible)

*   **A. ALOHA Protocols (Wireless origins):**
    *   **Pure ALOHA:** Transmit anytime. If no ACK (collision), wait random time, retransmit.
        *   Vulnerable Period: `2 * T_fr` (frame time).
        *   Max Throughput `S_max` ≈ 18.4% (`1/2e`). Very inefficient.
    *   **Slotted ALOHA:** Transmit only at slot start. Requires sync.
        *   Vulnerable Period: `1 * T_fr`.
        *   Max Throughput `S_max` ≈ 36.8% (`1/e`). Doubles Pure ALOHA.

*   **B. CSMA Protocols (Listen Before Talk - LANs):**
    *   **Persistence:**
        *   **1-Persistent:** If busy, wait till idle, then send. (Greedy, high collision risk).
        *   **Non-Persistent:** If busy, wait random time, then sense again. (Better, but can add delay).
        *   **p-Persistent:** If idle, send with prob `p`; else wait for next slot (1-p). (Compromise).
    *   **CSMA/CD (Collision Detection - Classic Ethernet):**
        1.  Sense channel (1-persistent).
        2.  If idle, transmit AND **listen while transmitting**.
        3.  If collision detected: Abort, send **Jam Signal**, use **BEB**, try again.
        4.  Must detect collision within `slot_time` (`2 * T_p`). Min frame size related to this.
        *   Good for wired shared media (half-duplex). Not in full-duplex switched Ethernet.
    *   **CSMA/CA (Collision Avoidance - Wi-Fi/802.11):**
        *   CD is hard in wireless (Hidden/Exposed Terminal, Fading).
        *   **Mechanisms:**
            *   **IFS (Interframe Spacing):** SIFS (for ACKs), DIFS (for data).
            *   **Contention Window (Random Back-off):** Wait random slots if channel idle after DIFS.
            *   **ACKs:** Explicit ACK for each unicast data frame.
            *   **RTS/CTS (Request to Send/Clear to Send):** Optional. Sender sends RTS, Receiver sends CTS. Others hear & set NAV (Network Allocation Vector) to defer. Helps with hidden terminal.
            *   **NAV:** Timer, stations defer if NAV > 0.

### 5. Collision-Free Protocols
(Ensure no collisions; more coordination)

*   **Basic Bit-Map (Reservation):**
    *   `N` reservation slots. Station `i` sets bit in slot `i` if it has data.
    *   Transmit in order of reservation.
    *   **Pros:** Good at high load. **Cons:** `N` bits overhead/cycle, bad at low load.
*   **Binary Countdown:**
    *   Stations broadcast address (MSB first). If own bit `0` is overridden by `1`, station drops out.
    *   Highest address wins and transmits.
    *   **Pros:** Efficient reservation. **Cons:** Prioritizes high addresses, needs bit-sync & OR-channel logic.
*   **Adaptive Tree Walk:**
    *   Stations in logical tree. Probe tree (root down) to find ready stations.
    *   If collision at a node, probe its children.
    *   **Pros:** Adapts to load. **Cons:** Complex.

### 6. Performance Metrics for MAC
*   **Throughput (S):** Successful data rate (fraction of capacity or bps).
*   **Delay (D):** Avg. time for frame (queueing, access, tx, prop, re-tx delay).
*   **Channel Utilization:** Fraction of time channel used for success.
*   **Fairness:** Equitable access.
*   **Stability:** Consistent performance under increasing load.
*   **Scalability:** Performance as N stations or area increases.
*   **Complexity:** Implementation difficulty.

### 7. IEEE 802 Series & Variants
(LLC + MAC sublayers)

*   **802.1:** Bridging, VLANs (802.1Q), Port Auth (802.1X), STP (802.1D).
*   **802.2:** LLC (historically, now often bypassed by EtherType for IP).
*   **802.3 Ethernet (CSMA/CD):**
    *   `10BASE5` (Thicknet), `10BASE2` (Thinnet), `10BASE-T` (Twisted Pair)
    *   `100BASE-TX` (Fast Eth), `1000BASE-T` (GigE)
    *   `10GBASE-T`, Fiber variants (SR, LR, ER) etc. for 10G, 40G, 100G+.
*   **802.11 Wi-Fi (CSMA/CA):**
    *   `802.11b` (11 Mbps, 2.4GHz), `802.11a` (54 Mbps, 5GHz), `802.11g` (54 Mbps, 2.4GHz)
    *   `802.11n` (Wi-Fi 4 - MIMO), `802.11ac` (Wi-Fi 5 - MU-MIMO, wider channels)
    *   `802.11ax` (Wi-Fi 6/6E - OFDMA, efficiency in dense areas, 6GHz)
    *   `802.11be` (Wi-Fi 7 - upcoming, EHT)
    *   Security: `802.11i` (WPA2/3). QoS: `802.11e`.
*   **802.15 WPAN:**
    *   `802.15.1` Bluetooth
    *   `802.15.4` Low-Rate WPAN (Zigbee, Thread)
*   **Obsolete/Less Common:** 802.4 Token Bus, 802.5 Token Ring, 802.16 WiMAX.

--- 
**Quick Exam Tips for Module 3:**
*   MAC Addresses: Structure (OUI), types (uni/multi/broadcast).
*   BEB: How it works, why exponential & random.
*   ALOHA (Pure vs. Slotted): Vulnerable period, max throughput. Basic idea.
*   CSMA: Persistence methods. CSMA/CD vs. CSMA/CA: Full operation, when/why used, key differences (jam signal vs. RTS/CTS, NAV, IFS).
*   Collision-Free: Basic idea of Bit-Map, Binary Countdown, Adaptive Tree Walk.
*   IEEE 802 Standards: Match common standards (802.3, 802.11, 802.15.1, 802.15.4, 802.1Q) to their technology/purpose. Know key Wi-Fi generations (b, g, n, ac, ax). 