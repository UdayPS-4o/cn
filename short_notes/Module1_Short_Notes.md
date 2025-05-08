# Module 1: Short Notes - Introduction to Computer Networks

## Key Concepts & Exam Quick Call

### 1. Network Components & Categories

*   **Network:** Interconnected devices for communication & resource sharing.
*   **Components (Remember "End In Media ProSoft"):**
    *   **End Devices:** PCs, Servers, Phones (where data starts/ends).
    *   **Intermediary Devices:** Switches (LAN, MAC), Routers (connects networks, IP), Hubs (old, dumb), APs (wireless), Modems (digital-analog), Firewalls (security).
    *   **Network Media:** Path for data (Wired: Twisted Pair, Coax, Fiber; Wireless: Radio, Micro, IR).
    *   **Protocols:** Rules (TCP/IP, HTTP).
    *   **Network Software:** NOS, Apps.
*   **Categories (Geographical - Remember "PLan MAN WANts GANts"):**
    *   **PAN (Personal):** ~Meters (Bluetooth, personal gadgets).
    *   **LAN (Local):** Building/Office (Ethernet, Wi-Fi, private).
    *   **MAN (Metropolitan):** City/Campus (Connects LANs, fiber).
    *   **WAN (Wide):** Country/Global (Internet, connects LANs/MANs, leased lines).
    *   **GAN (Global):** Often refers to the Internet or global satellite nets.
*   **Other Categories:**
    *   **Architecture:** Client-Server vs. Peer-to-Peer (P2P).
    *   **Access:** Intranet (private), Extranet (partners), Internet (public).

### 2. Network Topologies
(How devices are arranged: Physical vs. Logical)

*   **Bus:** Single backbone cable. **Pros:** Cheap, easy. **Cons:** Break = whole network down, security, performance degrades. (Think old bus route - one road for all).
*   **Ring:** Devices in a circle. Data passes sequentially. Token Ring common. **Pros:** Orderly, good under load (token). **Cons:** Single break = down, adding/removing disruptive. (Think passing a note in a circle).
*   **Star:** Central device (hub/switch). **Pros:** Easy to manage/add/remove, fault isolation (cable break only affects one). **Cons:** Central device failure = whole network down. (Modern LANs - everything connects to a central point).
*   **Mesh:** Devices interconnected (Full: all-to-all; Partial: some-to-some). **Pros:** Highly reliable, robust, secure. **Cons:** Very expensive, complex. (Think a net - many paths).
*   **Tree (Hierarchical):** Star networks connected to a bus backbone. **Pros:** Scalable, segmented. **Cons:** Backbone/root failure critical. (Think a family tree structure).
*   **Hybrid:** Combination of topologies. (Most real-world networks).
*   **Logical Topology:** How data *flows* (e.g., Wi-Fi is physical star, logical bus).

### 3. Reference Models: OSI & TCP/IP

**OSI Model (7 Layers - Remember: "Please Do Not Throw Sausage Pizza Away" - Physical to Application)**

1.  **Physical (Layer 1):** Bits, cables, signals, voltages. (Actual transmission).
    *   PDU: Bit.
    *   Keywords: Encoding, Media, Topology, Transmission rate.
2.  **Data Link (Layer 2):** Framing, MAC addresses, error control (hop-to-hop), flow control.
    *   PDU: Frame.
    *   Keywords: Ethernet, Wi-Fi, Switches, PPP, LLC, MAC sublayer.
3.  **Network (Layer 3):** IP addressing, routing, path determination, packets.
    *   PDU: Packet.
    *   Keywords: IP, Routers, ICMP, OSPF, BGP.
4.  **Transport (Layer 4):** End-to-end connection, reliability (TCP), UDP, port numbers, segmentation.
    *   PDU: Segment (TCP), Datagram (UDP).
    *   Keywords: TCP, UDP, Flow control, Error control (end-to-end).
5.  **Session (Layer 5):** Dialog control, synchronization, manages sessions.
    *   PDU: Data.
    *   Keywords: Establish, Manage, Terminate sessions, NetBIOS, RPC.
6.  **Presentation (Layer 6):** Data formatting, encryption, compression.
    *   PDU: Data.
    *   Keywords: ASCII, JPEG, SSL/TLS (encryption part).
7.  **Application (Layer 7):** User interface to network, network services.
    *   PDU: Data.
    *   Keywords: HTTP, FTP, SMTP, DNS, Browsers, Email.

**TCP/IP Model (4 Layers - Remember: "Armadillos Take In New Ants" - Application, Transport, Internet, Network Access)**

1.  **Network Access (Link):** Combines OSI Physical & Data Link. (Hardware, MAC, frames).
2.  **Internet:** OSI Network. (IP addresses, routing, packets).
3.  **Transport (Host-to-Host):** OSI Transport. (TCP, UDP, ports).
4.  **Application:** Combines OSI Application, Presentation, Session. (HTTP, FTP, etc.).

*   **Encapsulation:** Data -> Segment -> Packet -> Frame -> Bits (Down the stack, adding headers).
*   **Decapsulation:** Bits -> Frame -> Packet -> Segment -> Data (Up the stack, removing headers).

### 4. Principles of Physical Layer

*   **Transmission Media:** Path for signals.
    *   **Guided (Wired):**
        *   **Twisted Pair (UTP/STP):** Copper wires twisted. **Pros:** Cheap. **Cons:** Interference, distance. (LAN cables).
        *   **Coaxial Cable:** Central conductor, shield. **Pros:** Better shield/bandwidth than TP. **Cons:** Bulkier. (Cable TV/Internet).
        *   **Fiber Optic (SMF/MMF):** Light pulses in glass. **Pros:** HUGE bandwidth, no EMI, long distance. **Cons:** Expensive, fragile. (Backbones).
    *   **Unguided (Wireless):**
        *   **Radio Waves:** Omnidirectional (Wi-Fi, Bluetooth, Cellular).
        *   **Microwaves:** Line-of-sight (Terrestrial, Satellite).
        *   **Infrared (IR):** Short-range, line-of-sight (TV remotes).
*   **Bandwidth:** Capacity of a channel.
    *   **Analog:** Hz (frequency range).
    *   **Digital:** bps (data rate).
    *   **Nyquist:** Max rate (noiseless) = `2 * B * log2(L)`
    *   **Shannon:** Capacity (noisy) = `B * log2(1 + S/N)`
*   **Multiplexing:** Sharing a single channel.
    *   **FDM (Frequency Division):** Different frequencies for different signals (Analog TV/Radio). **Guard Bands needed.**
    *   **WDM (Wavelength Division):** Different wavelengths of light in fiber (DWDM, CWDM). Like FDM for light.
    *   **TDM (Time Division):** Time slots for different signals.
        *   **Synchronous TDM (STDM):** Fixed slots, can be wasteful. (T1 lines).
        *   **Asynchronous/Statistical TDM (ATDM/StatTDM):** Dynamic slots, more efficient.
    *   **CDM (Code Division Multiplexing / CDMA):** Unique codes for users, share entire spectrum (Cellular - 3G).
*   **Switching:** Forwarding data.
    *   **Circuit Switching:** Dedicated path established *before* data transfer. (Old Phones - PSTN).
        *   **Phases:** Establish, Transfer, Disconnect.
        *   **Pros:** Guaranteed bandwidth, constant delay. **Cons:** Inefficient for bursty data, setup delay.
    *   **Packet Switching:** Data in packets, routed independently (Datagram) or via virtual circuit (VC).
        *   **Datagram (Connectionless - IP):** No setup, packets may take different paths, out of order. **Pros:** Efficient, robust. **Cons:** Jitter, potential loss.
        *   **Virtual Circuit (Connection-Oriented - ATM, Frame Relay):** Setup logical path, packets follow same path. **Pros:** Combines benefits. **Cons:** VC setup.
        *   **Store-and-Forward:** Switches store packet, then forward.
    *   **Message Switching (Older):** Entire message stored then forwarded. **Cons:** High delay, large buffers.

--- 
**Quick Exam Tips for Module 1:**
*   Be able to draw and explain all topologies with pros/cons.
*   Clearly differentiate OSI layers with their functions, PDUs, and key protocols/devices.
*   Compare OSI and TCP/IP.
*   Explain different transmission media with pros/cons and use cases.
*   Understand the core idea behind each multiplexing technique.
*   Compare circuit vs. packet switching in detail (especially Datagram vs. VC).
*   Remember mnemonics for OSI layers! 