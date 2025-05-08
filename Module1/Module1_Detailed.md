# Module 1: Introduction to Computer Networks

## Network: Components and Categories

### What is a Network?
A **computer network** is a collection of interconnected autonomous computing devices (like computers, servers, printers, mobile phones) that can exchange data and share resources. The primary purpose of a network is to enable communication and resource sharing among its users.

**Key characteristics of a network:**
*   **Connectivity:** Devices can communicate with each other.
*   **Resource Sharing:** Allows sharing of hardware (printers, scanners), software (applications, databases), and data.
*   **Scalability:** Can grow to accommodate more users and devices.
*   **Reliability:** Ensures data is delivered accurately and consistently.
*   **Security:** Protects data and resources from unauthorized access.

### Network Components
A network is comprised of several key components:

1.  **End Devices (Hosts):**
    *   These are the devices that users interact with directly and that originate or terminate data.
    *   Examples: Personal computers (PCs), laptops, smartphones, servers, printers, IoT devices.
    *   Each end device typically has a unique address (e.g., IP address, MAC address) for identification on the network.

2.  **Intermediary Devices (Networking Devices):**
    *   These devices connect end devices to the network and manage data flow. They ensure data travels from its source to its destination.
    *   Examples:
        *   **Switches:** Connect devices within a local area network (LAN). They use MAC addresses to forward data to the correct device.
        *   **Routers:** Connect different networks together (e.g., a LAN to the internet). They use IP addresses to determine the best path for data.
        *   **Hubs (older technology):** Connect devices in a LAN but broadcast data to all connected devices, leading to inefficiencies. Largely replaced by switches.
        *   **Access Points (APs):** Allow wireless devices to connect to a wired network.
        *   **Modems (Modulator/Demodulator):** Convert digital signals from a computer into analog signals for transmission over telephone lines (e.g., DSL modems) or cable lines, and vice-versa.
        *   **Firewalls:** Security devices that monitor and control incoming and outgoing network traffic based on predetermined security rules.

3.  **Network Media (Transmission Media):**
    *   The physical path through which data travels from one device to another.
    *   **Wired Media:**
        *   **Twisted-Pair Cable:** (e.g., Ethernet cables like Cat 5e, Cat 6) Consists of pairs of insulated copper wires twisted together to reduce electromagnetic interference.
        *   **Coaxial Cable:** Has a central copper conductor surrounded by an insulator, then a braided metal shield, and an outer jacket. Used for cable TV and internet.
        *   **Fiber-Optic Cable:** Transmits data as pulses of light through thin strands of glass or plastic. Offers high bandwidth, long distances, and immunity to electromagnetic interference.
    *   **Wireless Media:**
        *   **Radio Waves:** Used for Wi-Fi, Bluetooth, cellular networks (3G, 4G, 5G). Data is transmitted through the air using electromagnetic waves.
        *   **Microwaves:** Used for long-distance point-to-point communication (e.g., satellite communication, terrestrial microwave links).
        *   **Infrared:** Used for short-range communication (e.g., TV remotes). Requires line-of-sight.

4.  **Network Protocols:**
    *   A set of rules and conventions that govern how data is formatted, transmitted, and received over a network.
    *   Protocols ensure that different devices and systems can communicate effectively.
    *   Examples: TCP/IP (Transmission Control Protocol/Internet Protocol), HTTP (Hypertext Transfer Protocol), FTP (File Transfer Protocol), Ethernet.

5.  **Network Software:**
    *   Includes operating systems with networking capabilities, network drivers, and applications that use the network.
    *   **Network Operating System (NOS):** Software that runs on servers and manages network resources (e.g., Windows Server, Linux).
    *   **Network Applications:** Software that users interact with to perform tasks over the network (e.g., web browsers, email clients, file sharing programs).

### Network Categories (Based on Geographical Span)

Networks are often categorized by their size and geographical reach:

1.  **Personal Area Network (PAN):**
    *   **Span:** Very small area, typically within a few meters around an individual.
    *   **Purpose:** Connects personal devices like smartphones, laptops, wireless headphones, smartwatches.
    *   **Technology:** Bluetooth, Zigbee, Infrared.
    *   **Example:** Connecting a smartphone to a wireless earbud.

2.  **Local Area Network (LAN):**
    *   **Span:** Limited geographical area, such as a single building, office, or home.
    *   **Purpose:** Connects devices within a private organization or residence for resource sharing and communication.
    *   **Ownership:** Typically privately owned and managed.
    *   **Technology:** Ethernet (wired), Wi-Fi (wireless).
    *   **Characteristics:** High data transfer rates, low latency.
    *   **Example:** Computers and printers in an office connected to share files and a common printer.

3.  **Metropolitan Area Network (MAN):**
    *   **Span:** Covers a larger geographical area than a LAN, such as a city or a large campus.
    *   **Purpose:** Interconnects multiple LANs within a city or metropolitan region. Can be owned by a single organization or a consortium, or provided as a public utility.
    *   **Technology:** Fiber optics, high-speed Ethernet, ATM (Asynchronous Transfer Mode), SMDS (Switched Multimegabit Data Service).
    *   **Example:** A cable TV network that also provides internet access across a city; a network connecting multiple branches of a university campus.

4.  **Wide Area Network (WAN):**
    *   **Span:** Spans a large geographical area, such as a country, continent, or even the entire globe.
    *   **Purpose:** Interconnects LANs and MANs over long distances.
    *   **Ownership:** Often involves multiple service providers and public infrastructure (e.g., telephone lines, satellite links).
    *   **Technology:** Leased lines (e.g., T1, E1), Frame Relay, ATM, MPLS (Multiprotocol Label Switching), satellite links, fiber optic cables. The Internet is the largest WAN.
    *   **Characteristics:** Lower data transfer rates and higher latency compared to LANs due to the distances involved.
    *   **Example:** A multinational corporation connecting its offices in different countries.

5.  **Global Area Network (GAN):**
    *   This term is sometimes used to refer to networks that span the entire globe, essentially the Internet itself or large international satellite networks. It's often considered a type of WAN.

### Other Network Classifications:

*   **Based on Topology:** (Discussed in the next section) How devices are physically or logically arranged.
*   **Based on Architecture:**
    *   **Client-Server:** A central server provides resources and services to client devices.
    *   **Peer-to-Peer (P2P):** All devices have equal capabilities and can act as both a client and a server.
*   **Based on Access:**
    *   **Intranet:** A private network within an organization, using internet protocols, accessible only by internal members.
    *   **Extranet:** An extension of an intranet that allows controlled access to authorized external users (e.g., business partners, customers).
    *   **Internet:** A global system of interconnected computer networks.

## Network Topologies

Network topology refers to the arrangement of the elements (links, nodes, etc.) of a communication network. It can be **physical** (how devices are physically connected with wires and cables) or **logical** (how data flows between devices, regardless of their physical connection).

### Physical Topologies:

1.  **Bus Topology:**
    *   **Description:** All devices are connected to a single common cable, called the backbone or bus.
    *   **Working:** When a device wants to transmit, it sends the data onto the bus. All other devices on the bus receive the data, but only the intended recipient processes it. Terminators are used at both ends of the bus to absorb signals and prevent reflection.
    *   **Advantages:**
        *   Easy to install and relatively inexpensive.
        *   Requires less cable than star or mesh.
    *   **Disadvantages:**
        *   Difficult to troubleshoot. A break in the cable can bring down the entire network.
        *   Performance degrades as more devices are added (due to increased traffic and collisions).
        *   Limited cable length and number of devices.
        *   All devices share the same bandwidth.
        *   Low security as all devices see all data.
    *   **Use Cases:** Older LANs (e.g., early Ethernet - 10BASE2, 10BASE5). Rarely used today.

2.  **Ring Topology:**
    *   **Description:** Devices are connected in a circular fashion, with each device connected directly to two other devices (one on either side).
    *   **Working:** Data travels in one direction (unidirectional) or sometimes both (bidirectional) around the ring. Each device receives the data, checks if it's the intended recipient. If not, it regenerates and passes the signal to the next device.
    *   **Token Ring:** A common implementation where a special frame (token) circulates. Only the device holding the token can transmit.
    *   **Advantages:**
        *   Orderly access to the medium (no collisions in token ring).
        *   Performs better than bus under heavy load if using token passing.
        *   Easy to identify faults (e.g., if a device doesn't pass the token).
    *   **Disadvantages:**
        *   A single break in the cable or a faulty device can disable the entire network (unless a dual ring or fault tolerance mechanism is used).
        *   Adding or removing devices can disrupt the network.
        *   More complex to install and manage than bus.
    *   **Use Cases:** FDDI (Fiber Distributed Data Interface), SONET/SDH.

3.  **Star Topology:**
    *   **Description:** All devices are connected to a central device, such as a hub, switch, or router.
    *   **Working:** All data passes through the central device. If the central device is a hub, it broadcasts the data to all connected devices. If it's a switch, it forwards the data only to the intended recipient.
    *   **Advantages:**
        *   Easy to install and manage.
        *   Easy to add or remove devices without disrupting the network.
        *   A break in a cable only affects the device connected to that cable, not the entire network.
        *   Easy to troubleshoot faults.
        *   Switched star topologies offer good performance and dedicated bandwidth per device.
    *   **Disadvantages:**
        *   If the central device fails, the entire network goes down (single point of failure).
        *   Requires more cable than bus or ring.
        *   The performance and capacity of the network depend on the central device.
    *   **Use Cases:** Most modern LANs (Ethernet, Wi-Fi with access points).

4.  **Mesh Topology:**
    *   **Description:** Every device is connected to every other device in the network (full mesh), or at least to multiple other devices (partial mesh).
    *   **Working:** Data can take multiple paths from source to destination.
    *   **Advantages:**
        *   **High Reliability/Fault Tolerance:** If one link or device fails, data can be rerouted through other paths.
        *   **Robust:** The network can withstand multiple failures.
        *   **Security and Privacy:** Point-to-point links make it harder to intercept data.
        *   Can handle high traffic due to multiple paths.
    *   **Disadvantages:**
        *   **Expensive:** Requires a lot of cabling and many I/O ports (n(n-1)/2 links for n devices in a full mesh).
        *   **Complex to install and manage.**
        *   **Scalability Issues:** Adding new devices becomes increasingly complex.
    *   **Use Cases:**
        *   **Full Mesh:** Rarely used for LANs due to cost and complexity. Sometimes used in WAN backbones or critical connections.
        *   **Partial Mesh:** More common in WANs for redundancy between important sites. Wireless mesh networks are also an example.

5.  **Tree Topology (Hierarchical Topology):**
    *   **Description:** A hybrid topology that combines characteristics of bus and star topologies. It has a root node, and other nodes are connected in a hierarchical (parent-child) fashion. Groups of star-configured networks are connected to a linear bus backbone.
    *   **Working:** Similar to star, but with multiple levels.
    *   **Advantages:**
        *   Scalable; easy to add new segments or devices.
        *   Easier to manage and maintain in segments.
        *   Fault isolation is relatively easy (if a segment's central device fails, only that segment is affected, unless it's a high-level device).
    *   **Disadvantages:**
        *   If the main backbone cable breaks, the entire network can be crippled.
        *   If a high-level central device (e.g., at the root or a major branching point) fails, large portions of the network can go down.
        *   More difficult to configure and wire than other topologies.
    *   **Use Cases:** Large networks, such as in corporations or educational institutions, where different departments might have their own star networks connected via a backbone. Cable TV networks often use a tree structure.

6.  **Hybrid Topology:**
    *   **Description:** A combination of two or more different basic topologies.
    *   **Example:** A star-bus network (multiple star networks connected using a bus backbone) or a star-ring network.
    *   **Advantages:**
        *   Combines the advantages of the constituent topologies.
        *   Flexible and reliable.
        *   Scalable.
    *   **Disadvantages:**
        *   Can be complex to design and manage.
        *   Can be expensive depending on the combination.
    *   **Use Cases:** Most large real-world networks are hybrid, designed to meet specific needs.

### Logical Topology:
Describes how data actually flows between devices, which might be different from the physical layout. For example:
*   **Logical Bus:** In a Wi-Fi network (physical star with an Access Point), all devices share the same wireless medium, so it often behaves like a logical bus (using CSMA/CA).
*   **Logical Ring:** Token Ring networks are physically often wired as a star (to a MAU - Multistation Access Unit), but logically operate as a ring.

## Reference Models: ISO/OSI Model and TCP/IP Protocol Suite

Reference models provide a conceptual framework to understand and design network architectures. They divide the complex task of networking into smaller, manageable layers, each with specific functions.

### ISO/OSI Reference Model
The **Open Systems Interconnection (OSI)** model was developed by the International Organization for Standardization (ISO). It's a 7-layer model.

**Mnemonic for Layers (Top to Bottom): All People Seem To Need Data Processing**
**Mnemonic for Layers (Bottom to Top): Please Do Not Throw Sausage Pizza Away**

1.  **Layer 7: Application Layer**
    *   **Function:** Provides the interface for applications to access network services. It deals with user-facing protocols.
    *   **Responsibilities:**
        *   Identifying communication partners.
        *   Determining resource availability.
        *   Synchronizing communication.
        *   User authentication and privacy.
    *   **Protocols/Services:** HTTP, HTTPS, FTP, SMTP, POP3, IMAP, DNS, Telnet, SSH, SNMP.
    *   **PDU (Protocol Data Unit):** Data or Message.

2.  **Layer 6: Presentation Layer**
    *   **Function:** Responsible for data translation, encryption/decryption, and compression. Ensures that data sent by the application layer of one system can be understood by the application layer of another system.
    *   **Responsibilities:**
        *   **Data Formatting/Translation:** Converts data between different character codes (e.g., ASCII to EBCDIC), data structures.
        *   **Encryption/Decryption:** Secures data for transmission.
        *   **Compression/Decompression:** Reduces the size of data to be transmitted for efficiency.
    *   **Examples:** SSL/TLS (for encryption), JPEG, MPEG, GIF, ASCII, EBCDIC.
    *   **PDU:** Data or Message.

3.  **Layer 5: Session Layer**
    *   **Function:** Establishes, manages, and terminates communication sessions between applications.
    *   **Responsibilities:**
        *   **Session Establishment, Maintenance, and Termination:** Creating a connection, keeping it alive, and closing it down gracefully.
        *   **Dialog Control:** Manages whose turn it is to transmit (e.g., half-duplex, full-duplex).
        *   **Synchronization:** Adds checkpoints (synchronization points) into data streams for recovery in case of failures.
    *   **Protocols/APIs:** NetBIOS, RPC (Remote Procedure Call), Sockets (session management aspects).
    *   **PDU:** Data or Message.

4.  **Layer 4: Transport Layer**
    *   **Function:** Provides reliable and transparent data transfer between end systems (host-to-host). It segments data from the upper layers and reassembles it at the destination.
    *   **Responsibilities:**
        *   **Segmentation and Reassembly:** Breaks large messages into smaller segments/datagrams and reassembles them.
        *   **Service Point Addressing (Port Addressing):** Ensures data is delivered to the correct process/application on the host using port numbers.
        *   **Connection Control:** Can be connection-oriented (e.g., TCP) or connectionless (e.g., UDP).
        *   **Flow Control:** Manages the rate of data transmission to prevent overwhelming the receiver.
        *   **Error Control:** Ensures reliable data delivery (e.g., through acknowledgments, retransmissions).
    *   **Protocols:** TCP (Transmission Control Protocol), UDP (User Datagram Protocol).
    *   **PDU:** Segment (for TCP), Datagram (for UDP).

5.  **Layer 3: Network Layer**
    *   **Function:** Responsible for logical addressing, routing, and path determination for data packets across multiple networks.
    *   **Responsibilities:**
        *   **Logical Addressing (IP Addressing):** Assigns unique logical addresses (e.g., IP addresses) to hosts.
        *   **Routing:** Determines the best path for data to travel from source to destination across interconnected networks using routing algorithms. Routers operate at this layer.
        *   **Packet Forwarding:** Moving packets from an incoming link to an outgoing link on a router.
        *   **Fragmentation and Reassembly (if needed):** Breaking packets into smaller units if the underlying network has a smaller MTU (Maximum Transmission Unit).
    *   **Protocols:** IP (Internet Protocol - IPv4, IPv6), ICMP (Internet Control Message Protocol), IGMP (Internet Group Management Protocol), ARP (Address Resolution Protocol - though often debated, it links L3 to L2), OSPF, BGP.
    *   **PDU:** Packet.

6.  **Layer 2: Data Link Layer**
    *   **Function:** Provides reliable data transfer between two directly connected nodes (hop-to-hop) over a physical medium.
    *   **Responsibilities:**
        *   **Framing:** Divides the stream of bits received from the network layer into manageable data units called frames. Adds headers and trailers to frames.
        *   **Physical Addressing (MAC Addressing):** Uses physical addresses (MAC addresses) to identify devices on the local network segment.
        *   **Flow Control:** Manages data flow between adjacent nodes to prevent overwhelming the receiver.
        *   **Error Control:** Detects and sometimes corrects errors that occur in the physical layer (using techniques like CRC).
        *   **Access Control:** Determines which device has control over the transmission medium at any given time (e.g., CSMA/CD for Ethernet).
    *   **Sublayers:**
        *   **Logical Link Control (LLC):** Interfaces with the network layer, handles error control and flow control.
        *   **Media Access Control (MAC):** Deals with physical addressing and access to the transmission medium.
    *   **Protocols/Standards:** Ethernet, Wi-Fi (802.11), PPP (Point-to-Point Protocol), Frame Relay, ATM. Switches operate at this layer.
    *   **PDU:** Frame.

7.  **Layer 1: Physical Layer**
    *   **Function:** Responsible for the transmission and reception of raw bit streams over a physical medium.
    *   **Responsibilities:**
        *   **Bit Representation:** Defines how bits (0s and 1s) are encoded into electrical, optical, or radio signals.
        *   **Data Rate (Transmission Rate):** Defines the number of bits sent per second.
        *   **Synchronization of Bits:** Ensures sender and receiver clocks are synchronized.
        *   **Physical Medium:** Defines characteristics of the transmission medium (cables, connectors, wireless frequencies).
        *   **Physical Topology:** How devices are physically connected.
        *   **Transmission Mode:** Simplex, half-duplex, full-duplex.
    *   **Examples:** Ethernet cables, fiber optics, radio waves, RS-232, DSL, USB physical interface. Hubs and repeaters operate at this layer.
    *   **PDU:** Bit (or Symbol).

### TCP/IP Protocol Suite (Internet Protocol Suite)
The TCP/IP model is a more practical model that predates the OSI model and is the foundation of the internet. It is typically described as a 4-layer or 5-layer model.

**Common 4-Layer TCP/IP Model:**

1.  **Layer 4: Application Layer**
    *   **Combines:** OSI Application, Presentation, and Session layers.
    *   **Function:** Provides applications with standardized data exchange.
    *   **Protocols:** HTTP, FTP, SMTP, DNS, SSH, Telnet, SNMP, RTP.

2.  **Layer 3: Transport Layer (Host-to-Host Layer)**
    *   **Corresponds to:** OSI Transport Layer.
    *   **Function:** Provides end-to-end communication services, including reliable data transfer (TCP) and unreliable datagram service (UDP).
    *   **Protocols:** TCP (Transmission Control Protocol), UDP (User Datagram Protocol).

3.  **Layer 2: Internet Layer (Network Layer)**
    *   **Corresponds to:** OSI Network Layer.
    *   **Function:** Responsible for addressing, packaging, and routing messages across networks.
    *   **Protocols:** IP (Internet Protocol - IPv4, IPv6), ICMP, IGMP, ARP.

4.  **Layer 1: Network Access Layer (Link Layer / Network Interface Layer)**
    *   **Combines:** OSI Data Link and Physical layers.
    *   **Function:** Responsible for how data is sent and received over the physical network. This includes MAC addressing, framing, and interaction with the physical hardware.
    *   **Protocols/Standards:** Ethernet, Wi-Fi, Token Ring, Frame Relay, PPP, device drivers.

**Comparison: OSI vs. TCP/IP**

| Feature          | OSI Model                                  | TCP/IP Model                                      |
| ---------------- | ------------------------------------------ | ------------------------------------------------- |
| **Development**  | Prescriptive, theoretical model            | Descriptive, based on working protocols          |
| **Layers**       | 7 Layers                                   | 4 (or 5) Layers                                   |
| **Emphasis**     | Clear distinction between services, interfaces, and protocols | Focus on protocols and implementation             |
| **Usage**        | Primarily a reference/educational tool     | Practical model used for the Internet            |
| **Flexibility**  | Can be rigid                               | More flexible                                     |
| **Protocols**    | Defines layers first, then fits protocols  | Protocols defined first, then model described     |
| **Session Layer**| Dedicated Session Layer                    | Handled by Application or Transport Layer        |
| **Presentation Layer**| Dedicated Presentation Layer             | Handled by Application Layer                     |

**Encapsulation and Decapsulation:**
*   **Encapsulation:** As data moves down the protocol stack (from Application to Physical), each layer adds its own header (and sometimes a trailer) containing control information. This process is called encapsulation.
    *   Application Layer: Message
    *   Transport Layer: Segments/Datagrams (TCP/UDP header + Message)
    *   Network Layer: Packets (IP header + Segment/Datagram)
    *   Data Link Layer: Frames (Frame header + Packet + Frame trailer)
    *   Physical Layer: Bits
*   **Decapsulation:** As data moves up the protocol stack at the receiving end, each layer removes its corresponding header/trailer and processes the information.

## Principles of Physical Layer

The Physical Layer (Layer 1) is the lowest layer in the OSI and TCP/IP models. It deals with the physical transmission of raw bits over a communication channel.

### Transmission Media
The means by which signals are propagated from one point to another. Classified into Guided (Wired) and Unguided (Wireless).

**1. Guided Media (Wired):**
Signals are guided along a solid medium.

*   **Twisted-Pair Cable:**
    *   **Structure:** Consists of two insulated copper wires twisted together in a helical form. Twisting reduces electromagnetic interference (EMI) from external sources and crosstalk from adjacent pairs.
    *   **Types:**
        *   **Unshielded Twisted Pair (UTP):** Most common type, used in Ethernet LANs (e.g., Cat 5e, Cat 6, Cat 6a). No additional shielding.
        *   **Shielded Twisted Pair (STP):** Has a metal foil or braided mesh covering each pair or the entire cable to provide better protection against EMI and crosstalk. More expensive and harder to install than UTP.
    *   **Advantages:** Inexpensive, easy to install, widely available.
    *   **Disadvantages:** Susceptible to interference (especially UTP), limited bandwidth and distance compared to other media.
    *   **Applications:** Telephone lines, LANs (Ethernet).

*   **Coaxial Cable:**
    *   **Structure:**
        1.  Central core conductor (usually copper).
        2.  Insulating layer (dielectric).
        3.  Braided metal shield (outer conductor) to reduce EMI and crosstalk.
        4.  Outer insulating jacket.
    *   **Types:**
        *   **Thicknet (10BASE5):** Older, rigid cable.
        *   **Thinnet (10BASE2):** More flexible than Thicknet.
        *   **RG-59:** Used for cable television.
        *   **RG-6:** Higher quality, used for cable TV and cable internet.
    *   **Advantages:** Better shielding and higher bandwidth than twisted-pair, can span longer distances.
    *   **Disadvantages:** More expensive and less flexible than UTP. Bulkier.
    *   **Applications:** Cable TV distribution, cable internet, older Ethernet LANs, long-distance telephone transmission.

*   **Fiber-Optic Cable:**
    *   **Structure:** Transmits data as pulses of light through thin strands (fibers) of glass or plastic.
        1.  **Core:** The central part of the fiber where light travels.
        2.  **Cladding:** A layer of material surrounding the core with a lower refractive index, causing light to be reflected back into the core (Total Internal Reflection).
        3.  **Buffer Coating:** Protective plastic layer.
        4.  **Strength Members & Outer Jacket:** Provide additional protection.
    *   **Light Sources:** LEDs (Light Emitting Diodes) or Lasers.
    *   **Types:**
        *   **Multimode Fiber (MMF):** Larger core diameter, allows multiple light paths (modes). Used with LEDs, suitable for shorter distances (e.g., within buildings). Less expensive.
        *   **Single-Mode Fiber (SMF):** Very small core diameter, allows only a single light path. Used with lasers, suitable for long distances (e.g., WANs, undersea cables). Higher bandwidth, more expensive.
    *   **Advantages:**
        *   Extremely high bandwidth (Gbps to Tbps).
        *   Immune to electromagnetic interference (EMI) and radio frequency interference (RFI).
        *   Low attenuation (signal loss), allowing for very long transmission distances without repeaters.
        *   High security (difficult to tap).
        *   Lightweight and smaller size compared to copper cables for similar capacity.
    *   **Disadvantages:**
        *   More expensive than copper cable (though costs are decreasing).
        *   Requires specialized installation and maintenance skills.
        *   More fragile than copper cables.
        *   Connecting fibers (splicing, connectors) is more complex.
    *   **Applications:** High-speed LAN backbones, WANs, MANs, internet backbone, undersea communication cables, telecommunications.

**2. Unguided Media (Wireless):**
Signals are broadcast through air or space without a physical conductor.

*   **Radio Waves:**
    *   **Frequency Range:** 3 kHz to 300 GHz.
    *   **Characteristics:** Omnidirectional (travel in all directions from the source), can penetrate walls (depending on frequency). Susceptible to interference.
    *   **Applications:**
        *   AM/FM Radio broadcasting.
        *   Television broadcasting (VHF, UHF).
        *   Wi-Fi (WLANs - e.g., 2.4 GHz, 5 GHz bands).
        *   Bluetooth (short-range communication).
        *   Cellular networks (2G, 3G, 4G LTE, 5G).
        *   Cordless phones.

*   **Microwaves:**
    *   **Frequency Range:** 1 GHz to 300 GHz (overlaps with high-frequency radio waves).
    *   **Characteristics:** Unidirectional (require line-of-sight between transmitter and receiver antennas). Antennas are typically parabolic (dish-shaped). Cannot penetrate walls well.
    *   **Types:**
        *   **Terrestrial Microwaves:** Used for point-to-point links between buildings, towers. Repeater stations may be needed for long distances due to Earth's curvature.
        *   **Satellite Microwaves:** A satellite acts as a repeater in space. Transmits signals between ground stations. Used for broadcasting, long-distance telephony, internet access in remote areas (VSAT).
    *   **Advantages:** High bandwidth, suitable for long distances.
    *   **Disadvantages:** Requires line-of-sight, susceptible to atmospheric conditions (e.g., rain fade), cost of towers/satellites.

*   **Infrared (IR):**
    *   **Frequency Range:** 300 GHz to 400 THz.
    *   **Characteristics:** Short-range, line-of-sight communication. Cannot penetrate walls.
    *   **Advantages:** Inexpensive, does not interfere with other radio signals (as it's contained within a room). Secure from eavesdropping from outside the room.
    *   **Disadvantages:** Limited range, line-of-sight requirement, cannot pass through obstacles.
    *   **Applications:** TV remotes, wireless mice/keyboards (older types), short-range device-to-device communication.

### Bandwidth
Bandwidth refers to the range of frequencies that a transmission medium can pass or a signal contains. It is a measure of the information-carrying capacity of a channel or medium.

*   **Analog Bandwidth:** Measured in Hertz (Hz). It is the difference between the highest and lowest frequencies that a channel can transmit. For example, if a channel can pass frequencies from 1000 Hz to 5000 Hz, its bandwidth is 4000 Hz (4 kHz).
*   **Digital Bandwidth (Data Rate or Bit Rate):** Measured in bits per second (bps). It represents the amount of data that can be transmitted over a channel in a given amount of time.
    *   Common units: kbps (kilobits per second), Mbps (megabits per second), Gbps (gigabits per second).

**Relationship between Analog and Digital Bandwidth:**
*   The digital data rate achievable over a channel is related to its analog bandwidth, the signal-to-noise ratio (SNR), and the encoding scheme used.
*   **Nyquist Theorem (for noiseless channels):**
    `Max Data Rate = 2 * B * log2(L)`
    Where `B` is the analog bandwidth (Hz) and `L` is the number of signal levels used.
*   **Shannon-Hartley Theorem (for noisy channels):**
    `Max Capacity (C) = B * log2(1 + S/N)`
    Where `B` is the analog bandwidth (Hz) and `S/N` is the signal-to-noise power ratio.

**Factors Affecting Bandwidth Utilization:**
*   Transmission medium characteristics.
*   Signal encoding techniques.
*   Noise levels.
*   Channel impairments (attenuation, distortion).

### Multiplexing
Multiplexing is a technique that allows multiple signals or data streams to be combined and transmitted over a single shared communication channel or medium. This improves the efficiency of the medium by allowing it to be shared by multiple users or services. A **multiplexer (MUX)** combines the signals at the sending end, and a **demultiplexer (DEMUX)** separates them at the receiving end.

**Categories of Multiplexing:**

**1. Frequency Division Multiplexing (FDM):**
    *   **Principle:** Divides the total bandwidth of the communication channel into several non-overlapping frequency sub-bands (channels). Each sub-band is allocated to a different signal.
    *   **How it works:** Each signal is modulated onto a different carrier frequency. The modulated signals are then combined. Guard bands (unused frequency spaces) are often used between adjacent channels to prevent interference.
    *   **Type of Signals:** Analog signals.
    *   **Examples:**
        *   Radio and television broadcasting (different stations broadcast on different frequencies).
        *   Cable TV (multiple channels carried over a single coaxial cable).
        *   Early telephone systems (multiple voice calls on a single trunk line).
    *   **Advantages:** Simple concept, well-established technology.
    *   **Disadvantages:** Bandwidth wastage due to guard bands. If a channel is not used, its allocated frequency band remains idle.

**2. Wavelength Division Multiplexing (WDM):**
    *   **Principle:** An analog multiplexing technique conceptually similar to FDM, but used for optical fiber communication. It combines multiple optical signals (light beams) of different wavelengths (colors) onto a single optical fiber.
    *   **How it works:** Each input optical signal is assigned a unique wavelength. These are combined using a MUX (e.g., a prism or diffraction grating) and transmitted over the fiber. A DEMUX separates the wavelengths at the receiver.
    *   **Types:**
        *   **Dense WDM (DWDM):** Allows for a large number of channels (e.g., 40, 80, or more) with very narrow spacing between wavelengths. Used for long-haul and high-capacity links.
        *   **Coarse WDM (CWDM):** Fewer channels with wider spacing. Less expensive, used for shorter distances.
    *   **Advantages:** Enormously increases the capacity of fiber optic cables. Each wavelength can carry an independent data stream.
    *   **Disadvantages:** Higher cost of components (lasers, filters, MUX/DEMUX).

**3. Time Division Multiplexing (TDM):**
    *   **Principle:** Divides the available transmission time on a channel among different users or signals. Each signal is allocated a specific time slot during which it can transmit.
    *   **How it works:** Data from different sources is interleaved in time. The MUX collects data from each source in a round-robin fashion and transmits it in its allocated time slot. The DEMUX separates the data based on timing.
    *   **Type of Signals:** Digital signals (can also be used for analog if sampled).
    *   **Types:**
        *   **Synchronous TDM (STDM):**
            *   Each input source is assigned a fixed time slot in every frame, regardless of whether it has data to send.
            *   If a source has no data, its time slot remains empty (wasted bandwidth).
            *   Simple to implement.
            *   Example: T1/E1 telephone carrier lines.
        *   **Asynchronous TDM (ATDM) / Statistical TDM (StatTDM):**
            *   Time slots are allocated dynamically on demand. Only sources with data to send are given time slots.
            *   More efficient use of bandwidth as idle slots are not wasted.
            *   Requires addressing information within the time slots to identify the source/destination.
            *   More complex than STDM.
            *   Example: Packet switching networks inherently use statistical multiplexing.
    *   **Advantages:** Efficient for digital signals. STDM is simple. ATDM offers better bandwidth utilization.
    *   **Disadvantages:** STDM can be wasteful if sources are bursty. ATDM is more complex and may introduce variable delays. Synchronization is critical.

**4. Code Division Multiplexing (CDM) / Code Division Multiple Access (CDMA):**
    *   **Principle:** Allows multiple users to transmit simultaneously over the entire frequency spectrum at the same time. Users are distinguished by unique codes.
    *   **How it works:** Each user's data signal is multiplied by a unique spreading code (orthogonal or pseudo-random code). This spreads the signal over a wider bandwidth. At the receiver, the same code is used to recover the original signal, while signals with other codes appear as noise and are filtered out.
    *   **Type of Signals:** Digital signals.
    *   **Advantages:**
        *   High capacity and efficient use of bandwidth.
        *   Resistance to interference and multipath fading.
        *   Soft handoff in cellular systems.
        *   Inherent security due to the unique codes.
    *   **Disadvantages:**
        *   Complex signal processing required.
        *   Precise power control is needed (near-far problem: a nearby strong signal can overwhelm a distant weaker signal if power isn't managed).
    *   **Applications:** Some cellular mobile communication systems (e.g., 2G IS-95, 3G UMTS/WCDMA), GPS.

### Switching
Switching is the process of forwarding data (packets, frames, cells, or circuits) from an input port to an output port of a switch, leading towards the destination. Switching techniques are essential for building larger networks by interconnecting multiple devices.

**Main Switching Techniques:**

**1. Circuit Switching:**
    *   **Principle:** A dedicated physical communication path (circuit) is established between the sender and receiver before data transmission begins. This path remains exclusive to the users for the duration of the communication.
    *   **Phases:**
        1.  **Circuit Establishment:** A dedicated path is set up from source to destination through various switches in the network. Resources (e.g., bandwidth, switch capacity) are reserved along this path.
        2.  **Data Transfer:** Data is transmitted along the dedicated path. No addressing information is needed in the data itself after the circuit is established, as the path is fixed.
        3.  **Circuit Disconnect:** The circuit is terminated when the communication is complete, and the reserved resources are released.
    *   **Characteristics:**
        *   Guaranteed bandwidth and constant data rate.
        *   Fixed delay (propagation delay + processing delay at switches during setup).
        *   No congestion once the circuit is established (for that circuit).
        *   Resources are dedicated, even if no data is being sent (can be inefficient).
        *   Call setup delay.
    *   **Examples:**
        *   Traditional Public Switched Telephone Network (PSTN).
        *   ISDN (Integrated Services Digital Network).
    *   **Advantages:** Suitable for real-time, continuous stream applications (e.g., voice calls, uncompressed video). Guaranteed quality of service.
    *   **Disadvantages:** Inefficient for bursty data traffic (resources are idle if no data). Long call setup time. If all circuits are busy, new connections are blocked.

**2. Packet Switching:**
    *   **Principle:** Data is divided into small units called packets. Each packet contains a header with source and destination address information, as well as control information. Packets are transmitted independently through the network and may take different routes to the destination.
    *   **How it works:**
        *   Packets from different sources are interleaved on shared network links (statistical multiplexing).
        *   Switches (routers in the context of IP networks) receive packets, store them temporarily (store-and-forward), and then forward them to the next hop based on the destination address in the packet header.
    *   **Types:**
        *   **Datagram Packet Switching (Connectionless):**
            *   Each packet is treated independently and routed separately.
            *   Packets may arrive out of order at the destination and may take different paths.
            *   No prior connection setup is required.
            *   The Internet (IP) is a datagram network.
        *   **Virtual Circuit Packet Switching (Connection-Oriented):**
            *   A logical path (Virtual Circuit - VC) is established between sender and receiver before data transmission, but resources are not dedicated exclusively.
            *   All packets belonging to a VC follow the same path.
            *   Packets contain a VC identifier (VCI) instead of full destination addresses, making headers smaller.
            *   Combines aspects of circuit switching (fixed path, ordered delivery for a given VC) and packet switching (shared resources).
            *   Examples: Frame Relay, ATM, MPLS.
            *   **Phases in VC:** Setup, Data Transfer, Teardown.
    *   **Advantages:**
        *   Efficient use of network bandwidth (resources are shared).
        *   Suitable for bursty data traffic.
        *   More robust to link failures (packets can be rerouted).
        *   No call setup delay in datagram networks (but VCs have setup).
    *   **Disadvantages:**
        *   Variable delay (jitter) and potential for packet loss due to congestion.
        *   Packets may arrive out of order (in datagram networks), requiring reordering at the destination.
        *   Requires more complex protocols for error control and flow control.
        *   Overhead due to packet headers.
    *   **Applications:** Most data networks, including the Internet.

**3. Message Switching (Older Technique):**
    *   **Principle:** Similar to packet switching, but the entire message is treated as a single unit. Messages are stored completely at each intermediate switch (store-and-forward) before being forwarded to the next switch.
    *   **How it works:** Each message contains destination address information. Switches store the entire message, check for resources/next hop availability, and then transmit.
    *   **Characteristics:**
        *   No physical path is established.
        *   Requires large buffers at switches to store entire messages.
        *   Can lead to long delays if messages are large.
        *   Better channel efficiency than circuit switching for bursty traffic.
    *   **Disadvantages:**
        *   High storage requirements at switches.
        *   Long delays, not suitable for real-time applications.
    *   **Applications:** Early telegraph systems, some email systems (historically). Largely superseded by packet switching.

**Comparison of Switching Techniques:**

| Feature           | Circuit Switching                       | Packet Switching (Datagram)          | Packet Switching (Virtual Circuit)   | Message Switching                     |
|-------------------|-----------------------------------------|--------------------------------------|--------------------------------------|---------------------------------------|
| **Path**          | Dedicated, fixed path                  | No dedicated path, dynamic routing   | Logical path (VC), fixed for session| No dedicated path, store & forward    |
| **Resource Allocation** | Reserved exclusively                 | Shared, on demand                    | Shared, but path established         | Shared, store & forward               |
| **Setup Phase**   | Required (Circuit establishment)       | Not required                         | Required (VC setup)                  | Not required                          |
| **Addressing**    | Not in data after setup               | Full source/dest in each packet      | VCI in each packet                   | Full source/dest in each message      |
| **Data Unit**     | Continuous stream                       | Packets                              | Packets                              | Messages                              |
| **Ordering**      | Preserved                               | Not necessarily preserved            | Preserved within a VC                | Preserved (message by message)        |
| **Delay**         | Constant (after setup)                  | Variable (jitter)                    | Less variable than datagram          | High, variable                        |
| **Congestion**    | Blocking during setup, none after     | Can occur, leads to delay/loss       | Can occur, flow control helps        | Can occur, leads to long queues       |
| **Efficiency**    | Low for bursty data                   | High for bursty data                 | High for bursty data                 | Moderate, better than circuit for bursty|
| **Primary Use**   | Voice (PSTN)                            | Internet (IP)                        | ATM, Frame Relay, MPLS               | Older data (e.g., telegraph)          |

This covers the topics in Module 1. Remember to focus on understanding the concepts, their advantages, disadvantages, and typical use cases. 