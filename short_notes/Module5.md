# Module 5: Transport & Application Layers (Short Notes & Revision)

## Key Concepts & Exam Points

### 1. Transport Layer: Core Responsibilities
*   **Primary Goal:** Process-to-process logical communication. Extends host-to-host (Network Layer) to specific applications on hosts.
*   **Key Functions & Design Issues:**
    *   **Addressing (Port Numbers):** 16-bit numbers (e.g., HTTP:80, FTP:21). Socket = IP + Port.
    *   **Multiplexing/Demultiplexing:** Combining/Separating data from/to multiple app processes using port numbers.
    *   **Reliability (TCP):** ACKs, sequence numbers, retransmissions.
    *   **Flow Control (TCP):** Prevents fast sender from swamping slow receiver (Sliding Window, `rwnd`).
    *   **Congestion Control (TCP):** Prevents network overload (Slow Start, Congestion Avoidance, `cwnd`, AIMD).
    *   **Connection Management (TCP):** 3-way handshake (SYN, SYN-ACK, ACK), 4-way termination (FINs, ACKs).

### 2. UDP (User Datagram Protocol)
*   **Transport Protocol:** Connectionless, unreliable, "best-effort".
*   **Header (8 bytes):** Source Port, Dest Port, Length, Checksum.
    *   **Checksum:** Optional in IPv4 (if 0, not used), mandatory in IPv6. Covers pseudo-header (IPs, proto, UDP len), UDP header, UDP data.
*   **Characteristics:**
    *   Low overhead, fast.
    *   Message-oriented (preserves boundaries).
    *   No flow/congestion control (application must handle if needed).
*   **Use Cases:** DNS, SNMP, DHCP (some messages), VoIP, video streaming (RTP often over UDP), online games.
    *   Good for real-time traffic where retransmission delay is worse than loss.

### 3. TCP (Transmission Control Protocol)
*   **Transport Protocol:** Connection-oriented, reliable, byte-stream.
*   **Header (min 20 bytes):** Source/Dest Port, Seq Num, Ack Num, Data Offset, Flags (URG, ACK, PSH, RST, SYN, FIN), Window (rwnd), Checksum, Urgent Pointer, Options.
    *   **Flags (Remember SAFR UP or similar):**
        *   `SYN`: Start connection.
        *   `ACK`: Acknowledge received data.
        *   `FIN`: Finish connection.
        *   `RST`: Reset/abort connection.
        *   `PSH`: Push data to app layer ASAP.
        *   `URG`: Urgent data present.
*   **Connection Management:**
    *   **Establishment (3-Way Handshake):** `Client (SYN, ISN_c)` -> `Server (SYN+ACK, ISN_s, ack ISN_c+1)` -> `Client (ACK, ack ISN_s+1)`.
    *   **Termination (4-Way Handshake):** `Side1 (FIN)` -> `Side2 (ACK)` -> `Side2 (FIN)` -> `Side1 (ACK)`. (TIME_WAIT state on side sending final ACK).
*   **Reliability Mechanisms:**
    *   **Sequence Numbers:** Order bytes, detect loss/duplicates.
    *   **Acknowledgements (Cumulative):** Ack N means bytes up to N-1 received.
    *   **Retransmission Timers (Adaptive RTO):** Resend if ACK not received.
    *   **Fast Retransmit:** 3 duplicate ACKs trigger retransmission before RTO.
*   **Flow Control (Sliding Window):** Receiver advertises `rwnd` (Receive Window). Sender ensures `LastByteSent - LastByteAcked <= min(rwnd, cwnd)`.
    *   **Zero Window Problem:** Solved by Persist Timer (sender sends probes).
    *   **Silly Window Syndrome:** Solved by Nagle's (sender) & Clark's/Delayed ACKs (receiver).
*   **Congestion Control (AIMD - Additive Increase, Multiplicative Decrease):**
    *   **`cwnd` (Congestion Window):** Sender-side limit on data in flight.
    *   **Slow Start (SS):** `cwnd` grows exponentially (doubles per RTT) until `ssthresh` or loss.
    *   **Congestion Avoidance (CA):** `cwnd` grows linearly (1 MSS per RTT) after `ssthresh`.
    *   **Loss Detection -> Action:**
        *   **Timeout:** `ssthresh = cwnd/2`, `cwnd = 1 MSS`, enter SS.
        *   **3 Dup ACKs (Reno):** `ssthresh = cwnd/2`, `cwnd = ssthresh + 3MSS` (Fast Recovery), then CA.
*   **TCP Timers:** RTO, Persist, Keepalive, TIME_WAIT (2*MSL).

### 4. Application Layer Protocols

*   **HTTP (Hypertext Transfer Protocol):** WWW. Client (browser) requests, Server responds. Uses TCP (port 80/HTTP, 443/HTTPS).
    *   Stateless (cookies add state).
    *   Methods: `GET`, `POST`, etc. Status Codes: `200 OK`, `404 Not Found`.
    *   HTTP/1.1: Persistent connections. HTTP/2: Multiplexing. HTTP/3: QUIC (UDP).
*   **FTP (File Transfer Protocol):** File transfer. Uses TCP. **Two connections:**
    *   **Control (Port 21):** Commands (`USER`, `PASS`, `LIST`, `RETR`, `STOR`).
    *   **Data (Port 20 active / dynamic passive):** Actual file/listing transfer.
    *   **Modes:** Active (server connects to client for data - firewall issues), Passive (client connects to server for data - preferred).
*   **SSH (Secure Shell):** Secure remote login, file transfer (SFTP, SCP), tunneling. Uses TCP (port 22).
    *   Provides: Authentication, Encryption, Integrity.
*   **DHCP (Dynamic Host Configuration Protocol):** Auto IP config (IP, mask, gateway, DNS). Uses UDP (Client:68, Server:67).
    *   **DORA Process:** **D**iscover (Client broadcast) -> **O**ffer (Server) -> **R**equest (Client broadcast) -> **A**ck (Server).
    *   Lease time for IP addresses.
*   **Email Protocols:**
    *   **SMTP (Simple Mail Transfer Protocol):** **Sending** email (MUA->MTA, MTA->MTA). TCP (port 25, 587/submission, 465/SMTPS).
        *   Text-based, push protocol. Commands: `HELO/EHLO`, `MAIL FROM`, `RCPT TO`, `DATA`, `QUIT`.
    *   **MIME (Multipurpose Internet Mail Extensions):** **Formats** email content (non-ASCII, attachments, multipart). Not a transfer protocol. Headers: `Content-Type`, `Content-Transfer-Encoding`.
    *   **POP3 (Post Office Protocol v3):** **Retrieving** email. TCP (port 110, 995/POP3S). Typically downloads & deletes from server.
    *   **IMAP (Internet Message Access Protocol):** **Accessing/managing** email on server. TCP (port 143, 993/IMAPS). Keeps mail on server, supports folders, flags. Better for multi-device access.
*   **DNS (Domain Name System):** Translates domain names to IP addresses. Uses UDP (port 53) mostly, TCP for large transfers.
    *   Hierarchical: Root -> TLD (`.com`) -> SLD (`example.com`) -> Subdomain.
    *   Servers: Root, TLD, Authoritative, Recursive (Caching).
    *   Resolution: Client -> Resolver -> Iterative queries (Root->TLD->Auth) -> IP to client.
    *   Records: `A` (IPv4), `AAAA` (IPv6), `MX` (Mail), `CNAME` (Alias), `NS` (Name Server), `PTR` (Reverse).
*   **SNMP (Simple Network Management Protocol):** Network device management. Uses UDP (Agent:161, Manager traps:162).
    *   Components: Manager (NMS), Agent (on device), Managed Device, MIB (database of OIDs).
    *   Operations: `GetRequest`, `SetRequest`, `Trap` (v1), `InformRequest` (v2c/v3).
    *   Versions: v1 (community strings - insecure), v2c (adds GetBulk), v3 (Security: Authentication, Encryption, Access Control).

## Mnemonics & Tips

*   **TCP vs. UDP:**
    *   **TCP = T**elephone **C**all **P**rotocol (connection setup, reliable, ordered like a conversation).
    *   **UDP = U D**on't care **P**rotocol (fire and forget, like sending a postcard).
*   **TCP 3-Way Handshake:** "Can I talk?" (SYN) -> "Yes, you can talk, can I talk?" (SYN-ACK) -> "Yes, you can talk." (ACK).
*   **DHCP DORA:** Like Dora the Explorer finding her way: **D**iscover, **O**ffer, **R**equest, **A**cknowledge.
*   **FTP Ports:** Control is like commanding (21), Data is like the main work (20 for active).
*   **SMTP for Sending, POP/IMAP for Receiving/Accessing:** Think of SMTP as the *Mailman* putting mail *into* the system, POP/IMAP as *you* going to the *Post Office/Mailbox* to get it.
*   **Application Layer Protocols - Well-Known Ports (Common Ones):
    *   FTP: 20 (Data Active), 21 (Control)
    *   SSH: 22
    *   SMTP: 25
    *   DNS: 53 (UDP/TCP)
    *   DHCP: 67 (Server), 68 (Client)
    *   HTTP: 80
    *   POP3: 110
    *   IMAP: 143
    *   HTTPS: 443

Key exam areas: TCP connection/termination, TCP reliability features, TCP flow/congestion control mechanisms (distinguish them!), UDP header and use cases, purpose and basic operation of each application layer protocol, especially their ports and transport protocols used. 