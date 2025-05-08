# Module 5: Transport and Application Layers (Detailed Notes)

## 1. Transport Layer

The transport layer is the fourth layer in the OSI model and the TCP/IP model. It provides logical communication between application processes running on different hosts. While the network layer provides host-to-host delivery, the transport layer provides process-to-process delivery.

### 1.1. Transport Layer Design Issues

1.  **Addressing (Port Numbers):**
    *   Identifying specific application processes on a host. The network layer delivers to a host, but multiple processes might be running.
    *   **Port Numbers:** 16-bit numbers used to identify processes. A combination of IP address + port number creates a **socket** (e.g., `192.168.1.100:80`).
    *   **Well-known ports (0-1023):** Reserved for standard services (e.g., HTTP:80, FTP:21, SMTP:25).
    *   **Registered ports (1024-49151):** Can be registered by software vendors.
    *   **Dynamic/Private/Ephemeral ports (49152-65535):** Used by clients for temporary connections.

2.  **Connection Establishment & Termination:**
    *   For connection-oriented services (like TCP), mechanisms are needed to:
        *   Establish a connection (e.g., TCP's 3-way handshake).
        *   Gracefully terminate a connection (e.g., TCP's FIN exchange).
    *   This involves negotiation of parameters (e.g., sequence numbers, window sizes).

3.  **Reliability:**
    *   Ensuring data arrives correctly and in order, without loss or duplication.
    *   Mechanisms:
        *   **Acknowledgements (ACKs):** Receiver confirms receipt of data.
        *   **Sequence Numbers:** To detect lost/duplicate packets and reorder out-of-order packets.
        *   **Retransmission Timers:** Sender retransmits data if ACK not received within a certain time.
        *   **Checksums:** For error detection in the segment header and data.

4.  **Flow Control:**
    *   Preventing a fast sender from overwhelming a slow receiver.
    *   Mechanisms (e.g., TCP's Sliding Window Protocol):
        *   Receiver advertises its available buffer space (receive window).
        *   Sender adjusts its sending rate based on this window.

5.  **Congestion Control:**
    *   Preventing network overload by too much traffic from multiple sources.
    *   Distinct from flow control (which is end-to-end between one sender/receiver).
    *   Mechanisms (e.g., TCP's algorithms: Slow Start, Congestion Avoidance, Fast Retransmit/Recovery):
        *   Sender infers congestion from packet loss or increased delay.
        *   Sender reduces its sending rate (congestion window).

6.  **Multiplexing and Demultiplexing:**
    *   **Multiplexing (Sender):** Gathering data from multiple application processes, adding transport headers (with port numbers), and passing segments to the network layer.
    *   **Demultiplexing (Receiver):** Receiving segments from the network layer and delivering data to the correct application process based on port numbers.

7.  **Error Control:**
    *   Detecting and sometimes correcting errors that occur during transmission.
    *   Primarily detection (via checksums). Correction is usually handled by retransmission.

8.  **Service Primitives:**
    *   Operations provided to the application layer to use transport services (e.g., `CONNECT`, `LISTEN`, `SEND`, `RECEIVE`, `DISCONNECT`).

### 1.2. UDP (User Datagram Protocol)
UDP is a simple, connectionless transport layer protocol. It provides a lightweight mechanism for process-to-process communication with minimal overhead.

**Characteristics:**
*   **Connectionless:** No handshake before sending data. Each UDP datagram is independent.
*   **Unreliable:** No guarantee of delivery, order, or duplicate protection. "Best-effort" delivery.
*   **Low Overhead:** Minimal header size (8 bytes).
*   **Fast:** No delays for connection setup or retransmissions at the transport layer.
*   **Message-Oriented:** Preserves message boundaries. If an application sends a 100-byte message, UDP will send one 100-byte datagram (if it fits). Contrast with TCP which is stream-oriented.
*   **No Flow Control or Congestion Control (inherently):** Relies on applications or other mechanisms if these are needed.

**When to Use UDP:**
*   Applications where speed and low overhead are critical, and occasional data loss is acceptable or handled by the application.
*   Real-time applications (e.g., streaming video/audio, online gaming) where retransmitting old data is not useful.
*   Query-response protocols (e.g., DNS, SNMP).
*   Applications that implement their own reliability and flow/congestion control (e.g., QUIC).
*   Multicasting and Broadcasting (TCP is point-to-point only).

#### 1.2.1. UDP Header Format
```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|            Length             |           Checksum            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                             Data                              ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

*   **Source Port (16 bits):** Identifies the port of the sending application process. Optional; if not used, set to zero. Useful for the receiver to send replies.
*   **Destination Port (16 bits):** Identifies the port of the receiving application process. Mandatory.
*   **Length (16 bits):** Length of the UDP header + UDP data in bytes. Minimum value is 8 (for a UDP datagram with no data).
*   **Checksum (16 bits):** Error detection for the UDP header, UDP data, and a **pseudo-header**.
    *   The pseudo-header includes the source IP, destination IP, protocol number (17 for UDP), and UDP length field. This helps detect misdelivered packets.
    *   Checksum calculation is optional in IPv4 but mandatory in IPv6 (if UDP is used with IPv6).
    *   If not calculated (IPv4 only), it should be set to all zeros. If the calculated checksum is `0xFFFF`, it is transmitted as `0xFFFF`. If the calculated checksum is `0x0000`, it is transmitted as `0xFFFF` (to distinguish from an unused checksum).

#### 1.2.2. Per-Segment Checksum (UDP Checksum Details)
*   The checksum is computed over:
    1.  **UDP Pseudo-Header:**
        *   Source IP Address (from IP Header)
        *   Destination IP Address (from IP Header)
        *   Zero byte (8 bits)
        *   Protocol field (from IP Header, value 17 for UDP)
        *   UDP Length field (from UDP Header)
    2.  **UDP Header**
    3.  **UDP Data (Payload)**
        *   If the data length is odd, a padding byte of zeros is added at the end for checksum calculation (but not transmitted).
*   **Calculation:** Uses 1's complement arithmetic. All 16-bit words are summed. The 1's complement of the final sum is the checksum.
*   **Verification:** Receiver performs the same calculation including the received checksum. If the result is all 1s (or a specific value like `0xFFFF` if that convention is used), the segment is considered error-free.

#### 1.2.3. Carrying Unicast/Multicast Real-Time Traffic with UDP
UDP is well-suited for real-time traffic due to its low overhead and lack of retransmission delays.
*   **Unicast Real-Time Traffic:**
    *   Examples: VoIP (Voice over IP), some forms of video conferencing.
    *   Application layer often handles minor packet loss (e.g., interpolation for audio, error concealment for video).
    *   Protocols like RTP (Real-time Transport Protocol) often run over UDP to provide sequence numbering, timestamping, and payload type identification for real-time data.
*   **Multicast Real-Time Traffic:**
    *   Examples: IPTV, live streaming to multiple recipients, stock market data feeds.
    *   UDP's inherent support for multicast (via IP multicast) makes it efficient. TCP cannot be used for IP multicast as TCP requires a point-to-point connection.
    *   Again, RTP is commonly used on top of UDP for these applications.
    *   Loss can be a bigger issue in multicast if not handled (e.g., via Forward Error Correction (FEC) at the application layer or by requesting retransmissions from other group members - NACK-Oriented Reliable Multicast - NORM).

**Challenges for Real-Time Traffic over UDP:**
*   **Jitter:** Variation in packet arrival times. Requires jitter buffers at the receiver.
*   **Packet Loss:** Can degrade quality. Application-level recovery or tolerance is needed.
*   **Out-of-Order Delivery:** Application must be able to handle or reorder if necessary (RTP helps with sequence numbers).

### 1.3. TCP (Transmission Control Protocol)
TCP is a connection-oriented, reliable transport layer protocol. It provides a byte-stream service, ensuring that data is delivered accurately, in order, and without duplication from a sender process to a receiver process.

**Characteristics:**
*   **Connection-Oriented:** Requires a 3-way handshake to establish a connection before data transfer, and a formal termination process.
*   **Reliable:** Guarantees delivery using sequence numbers, acknowledgements (ACKs), and retransmissions.
*   **Ordered Data Delivery:** Delivers data to the application in the same order it was sent.
*   **Byte-Stream Service:** Treats data as a continuous stream of bytes, not as individual messages. Application message boundaries are not preserved by TCP itself.
*   **Full-Duplex:** Data can flow in both directions simultaneously over a single connection.
*   **Flow Control:** Uses a sliding window mechanism to prevent sender from overwhelming receiver.
*   **Congestion Control:** Implements algorithms to reduce sending rate when network congestion is detected.
*   **Higher Overhead:** Larger header (minimum 20 bytes) and more processing due to connection management and reliability features.

#### 1.3.1. TCP Connection Management

**1. Connection Establishment (Three-Way Handshake):**
*   **Goal:** Synchronize sequence numbers and agree on connection parameters.
*   **Steps:**
    1.  **SYN (Client to Server):**
        *   Client sends a TCP segment with the `SYN` (Synchronize) flag set.
        *   Client chooses an Initial Sequence Number (ISN_c).
        *   Segment: `SYN=1, Seq=ISN_c`
    2.  **SYN-ACK (Server to Client):**
        *   Server receives SYN, allocates resources.
        *   Server sends a TCP segment with `SYN=1` and `ACK=1` (Acknowledge) flags set.
        *   Server chooses its own Initial Sequence Number (ISN_s).
        *   Acknowledges client's SYN: `Ack_num = ISN_c + 1`.
        *   Segment: `SYN=1, ACK=1, Seq=ISN_s, Ack_num=ISN_c+1`
    3.  **ACK (Client to Server):**
        *   Client receives SYN-ACK.
        *   Client sends a TCP segment with `ACK=1` flag set.
        *   Acknowledges server's SYN: `Ack_num = ISN_s + 1`.
        *   Segment: `ACK=1, Seq=ISN_c+1, Ack_num=ISN_s+1`
*   Connection is now established. Data transfer can begin.

**2. Data Transfer:**
*   Data is exchanged using sequence numbers and acknowledgements.
*   Each byte of data is numbered.
*   ACKs are typically cumulative (acknowledging all bytes up to a certain sequence number).

**3. Connection Termination (Four-Way Handshake - typically):**
*   Can be initiated by either side.
*   **Steps (assuming Client initiates):**
    1.  **FIN (Client to Server):**
        *   Client finishes sending data, sends a TCP segment with `FIN` (Finish) flag set.
        *   Segment: `FIN=1, Seq=X` (X is last data seq num + 1)
        *   Client enters `FIN_WAIT_1` state.
    2.  **ACK (Server to Client):**
        *   Server receives FIN, acknowledges it.
        *   Segment: `ACK=1, Ack_num=X+1`
        *   Server enters `CLOSE_WAIT` state. Client enters `FIN_WAIT_2` state.
        *   Server can still send any remaining data to the client.
    3.  **FIN (Server to Client):**
        *   Server finishes sending its data, sends its own `FIN` segment.
        *   Segment: `FIN=1, Seq=Y` (Y is server's last data seq num + 1)
        *   Server enters `LAST_ACK` state.
    4.  **ACK (Client to Server):**
        *   Client receives server's FIN, acknowledges it.
        *   Segment: `ACK=1, Ack_num=Y+1`
        *   Client enters `TIME_WAIT` state (waits for 2*MSL - Maximum Segment Lifetime - to ensure ACK is received and to handle delayed packets).
        *   Server receives ACK, connection is closed. Server releases resources.
*   After `TIME_WAIT` expires, client also closes the connection and releases resources.

**Simultaneous Close:** Both sides send FIN at the same time. Results in a sequence like FIN -> ACK -> FIN -> ACK.
**Half-Close:** One side sends FIN, indicating it won't send more data, but can still receive. The other side can continue sending data before sending its own FIN.

#### 1.3.2. Reliability of Data Transfers in TCP
TCP ensures reliable data transfer through several mechanisms:

1.  **Sequence Numbers:**
    *   Each byte of data transmitted in a TCP connection is assigned a unique sequence number.
    *   The initial sequence number (ISN) is chosen randomly during connection setup.
    *   Used to:
        *   Detect lost segments.
        *   Reorder out-of-order segments at the receiver.
        *   Detect and discard duplicate segments.

2.  **Acknowledgements (ACKs):**
    *   The receiver sends ACK segments back to the sender to confirm receipt of data.
    *   TCP uses **cumulative acknowledgements**: An ACK with `Ack_num = N` means all bytes up to `N-1` have been correctly received.
    *   **Delayed ACKs:** To reduce overhead, a receiver might wait a short period (e.g., up to 500ms or for more data to arrive to piggyback ACK) before sending an ACK. Can interact with retransmission timers.

3.  **Retransmission Timers (RTO - Retransmission Timeout):**
    *   When the sender transmits a segment, it starts a retransmission timer.
    *   If an ACK for that segment is not received before the timer expires, the sender assumes the segment (or its ACK) was lost and retransmits the segment.
    *   **Adaptive RTO:** The RTO value is dynamically adjusted based on the measured Round-Trip Time (RTT) between sender and receiver.
        *   `SRTT (Smoothed RTT) = (1 - alpha) * SRTT + alpha * SampleRTT`
        *   `RTTVAR (RTT Variation) = (1 - beta) * RTTVAR + beta * |SampleRTT - SRTT|`
        *   `RTO = SRTT + 4 * RTTVAR` (Karn's Algorithm: Don't sample RTT for retransmitted segments).

4.  **Checksum:**
    *   Each TCP segment includes a checksum covering the TCP header, TCP data, and a pseudo-header (similar to UDP, including source/destination IP, protocol=6, TCP length). Used to detect corrupted segments.

5.  **Duplicate Segment Detection:**
    *   If the receiver gets a segment with sequence numbers it has already acknowledged, it discards the duplicate and re-sends the last ACK.

6.  **Out-of-Order Segment Handling:**
    *   The receiver buffers out-of-order segments.
    *   When an in-sequence segment arrives filling a gap, the receiver can then pass the contiguous block of data to the application.
    *   **Fast Retransmit:** If a sender receives multiple (typically 3) duplicate ACKs for the same sequence number, it infers that the segment immediately following that acknowledged sequence number was lost, and retransmits it immediately without waiting for the RTO to expire. This is a key part of TCP congestion control.

#### 1.3.3. TCP Flow Control
Prevents a fast sender from overwhelming a slow receiver's buffer.

*   **Sliding Window Protocol:**
    *   The receiver advertises its current available buffer space in the **Receive Window (`rwnd`)** field of the TCP header it sends back to the sender.
    *   The sender maintains a **Send Window**, which cannot exceed the receiver's advertised `rwnd` (and also the sender's congestion window `cwnd`).
    *   The sender can send data up to its Send Window size without waiting for an ACK for each segment, but it must stop if the window fills up.
    *   As the receiver processes data and frees up buffer space, it sends ACKs with updated `rwnd` values.
*   **How it works:**
    1.  Receiver has a buffer for incoming data.
    2.  Receiver tells sender `rwnd` = amount of free buffer space.
    3.  Sender ensures `LastByteSent - LastByteAcked <= rwnd`.
    4.  If `rwnd` becomes 0, the sender must stop sending data (except for special probe segments).
*   **Zero Window Problem:**
    *   If receiver advertises `rwnd=0`, sender stops.
    *   If the subsequent ACK from receiver (with `rwnd > 0`) is lost, sender might wait indefinitely.
    *   **Solution: TCP Persist Timer.** If sender receives `rwnd=0`, it starts a persist timer. When it expires, sender sends a small **window probe** segment (1 byte of data or just header). This forces the receiver to re-advertise its `rwnd`.
*   **Silly Window Syndrome:**
    *   Problem: Small amounts of data are sent, leading to inefficient use of bandwidth (e.g., sender sends 1 byte, receiver ACKs, sender sends another 1 byte).
    *   **Sender-side solution (Nagle's Algorithm):** If there is unacknowledged data in flight, buffer small outgoing segments until an ACK arrives or a full-sized segment can be sent. Don't send tinygrams if previous ones aren't ACKed.
    *   **Receiver-side solution (Clark's Algorithm / Delayed ACKs):** Don't send a window update for a tiny space unless a significant portion of the buffer is free or a full-sized segment can be accommodated. Don't ACK every segment immediately; delay ACKs slightly to see if more data arrives to ACK together, or if a window update can be piggybacked.

#### 1.3.4. TCP Congestion Control
Aims to prevent the TCP sender from overwhelming the network, rather than just the receiver.
TCP infers congestion from packet loss (timeout or duplicate ACKs).

*   **Congestion Window (`cwnd`):**
    *   Maintained by the sender.
    *   Limits the amount of data the sender can have in transit (unacknowledged) independently of `rwnd`.
    *   Effective window = `min(cwnd, rwnd)`.

*   **Phases/Algorithms:**
    1.  **Slow Start (SS):**
        *   When a connection begins, or after a retransmission timeout.
        *   `cwnd` is initialized to a small value (e.g., 1, 2, or 4 MSS - Maximum Segment Size).
        *   For each ACK received, `cwnd` increases exponentially (e.g., `cwnd += 1 MSS` for each ACK, effectively doubling `cwnd` per RTT).
        *   Continues until `cwnd` reaches a **Slow Start Threshold (`ssthresh`)** or packet loss occurs.

    2.  **Congestion Avoidance (CA):**
        *   Once `cwnd` >= `ssthresh`, TCP enters congestion avoidance.
        *   `cwnd` increases additively (linearly) to probe for more available bandwidth gently.
        *   Typically, `cwnd += (MSS * MSS) / cwnd` per ACK (approximately 1 MSS per RTT).
        *   Continues until packet loss is detected.

    3.  **Congestion Detection and Response:**
        *   **Timeout Occurs (Severe Congestion):**
            *   `ssthresh` is set to `max(2 * MSS, cwnd / 2)`.
            *   `cwnd` is reset to 1 MSS (or initial small value).
            *   TCP re-enters Slow Start.
        *   **Three Duplicate ACKs Received (Mild Congestion - Fast Retransmit and Fast Recovery):**
            *   This indicates a single packet was likely lost, but subsequent packets are arriving.
            *   **Tahoe TCP (Older):** Treats this like a timeout: `ssthresh = cwnd/2`, `cwnd = 1 MSS`, enter Slow Start.
            *   **Reno TCP (More Common):** Implements Fast Recovery.
                *   `ssthresh = max(2 * MSS, cwnd / 2)`.
                *   `cwnd = ssthresh + 3 * MSS` (inflates window to account for the 3 duplicate ACKs).
                *   Retransmit the missing segment (Fast Retransmit).
                *   For each additional duplicate ACK, `cwnd += 1 MSS` (inflates window further as packets leave network).
                *   When ACK for new data arrives: `cwnd = ssthresh` (deflate window), enter Congestion Avoidance.
            *   **NewReno TCP:** Improves upon Reno by handling multiple packet losses within a single window more effectively.
            *   **SACK (Selective Acknowledgement):** An option that allows receiver to tell sender exactly which segments have been received, helping recover from multiple losses more efficiently.

*   **Additive Increase, Multiplicative Decrease (AIMD):**
    *   The general strategy: Increase `cwnd` additively during Congestion Avoidance, decrease it multiplicatively (halve it) when congestion is detected.
    *   Helps TCP connections to fairly share network bandwidth.

#### 1.3.5. TCP Header Format
```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        Sequence Number                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Acknowledgment Number                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Data |           |U|A|P|R|S|F|                               |
| Offset| Reserved  |R|C|S|S|Y|I|            Window             |
|       |           |G|K|H|T|N|N|                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Checksum            |         Urgent Pointer        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options (if Data Offset > 5)               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                             Data                              ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

*   **Source Port (16 bits):** Port number of the sending application.
*   **Destination Port (16 bits):** Port number of the receiving application.
*   **Sequence Number (32 bits):** Sequence number of the first data byte in this segment (unless SYN is present). If SYN is present, this is the Initial Sequence Number (ISN) and the first data byte is ISN+1.
*   **Acknowledgment Number (32 bits):** If ACK flag is set, this field contains the value of the next sequence number the sender of the ACK is expecting to receive.
*   **Data Offset (4 bits):** Length of the TCP header in 32-bit words. Minimum value is 5 (20 bytes header, no options). Maximum is 15 (60 bytes header).
*   **Reserved (6 bits):** Must be zero.
*   **Flags (6 bits - from left to right in diagram, though often listed as UAPRSF or CWR, ECE, URG, ACK, PSH, RST, SYN, FIN in other docs):**
    *   `URG`: Urgent Pointer field is significant.
    *   `ACK`: Acknowledgment Number field is significant.
    *   `PSH`: Push function. Asks receiver to push data to application ASAP.
    *   `RST`: Reset the connection (abort due to error).
    *   `SYN`: Synchronize sequence numbers (used in connection establishment).
    *   `FIN`: Finish. No more data from sender (used in connection termination).
    *   (Newer flags: CWR - Congestion Window Reduced, ECE - ECN-Echo - for Explicit Congestion Notification)
*   **Window (or Receive Window) (16 bits):** Number of data bytes the sender of this segment is willing to accept, beginning with the byte indicated in the Acknowledgment Number field. Used for flow control.
*   **Checksum (16 bits):** Error detection for TCP header, TCP data, and pseudo-header.
*   **Urgent Pointer (16 bits):** If URG flag is set, this field is an offset from the sequence number indicating the last byte of urgent data. Points to the end of the urgent data + 1.
*   **Options (variable length, up to 40 bytes):** Used for various extensions. Padded with zeros to ensure header is a multiple of 32 bits.
    *   Common Options:
        *   **Maximum Segment Size (MSS):** Sender indicates max segment size it can receive. Exchanged during SYN.
        *   **Window Scale Option:** Allows use of larger receive windows (beyond 2^16 - 1) for high-speed networks.
        *   **SACK (Selective Acknowledgment):** Allows receiver to specify non-contiguous blocks of received data.
        *   **Timestamps:** Used for RTT measurement and protection against wrapped sequence numbers (PAWS).
*   **Data (variable length):** Payload from the application layer.

#### 1.3.6. TCP Timer Management
TCP uses several timers to manage connections and ensure reliability:

1.  **Retransmission Timer (RTO Timer):**
    *   Governs retransmission of unacknowledged segments.
    *   Value is adaptive, based on RTT measurements.
    *   Started when a segment is sent; stopped/reset when its ACK is received.
    *   If it expires, segment is retransmitted, RTO is often backed off (doubled for exponential backoff).

2.  **Persist Timer:**
    *   Used to prevent deadlock if a receiver's window update (from `rwnd=0` to `rwnd>0`) is lost.
    *   When sender receives `rwnd=0`, it starts persist timer.
    *   On expiry, sender sends a window probe segment to elicit a window update from receiver.

3.  **Keepalive Timer:**
    *   Detects idle connections that are no longer active (e.g., client crashed without proper termination).
    *   If a connection is idle for a long period (e.g., 2 hours), sender sends keepalive probes.
    *   If no response after several probes, connection is assumed broken and terminated.
    *   Optional; not all implementations use it or enable by default.

4.  **TIME_WAIT Timer (2MSL Timer):**
    *   After sending the final ACK in connection termination (e.g., client after receiving server's FIN and sending its ACK), the side initiating the close (or the side that sent the first FIN if simultaneous) enters `TIME_WAIT` state.
    *   Duration is typically 2 * MSL (Maximum Segment Lifetime, e.g., MSL=60s, so TIME_WAIT=120s).
    *   **Purposes:**
        1.  Ensures the final ACK was received by the other side. If lost, the other side will retransmit its FIN, and the host in TIME_WAIT can re-send the ACK.
        2.  Prevents delayed segments from an old incarnation of the connection from being misinterpreted as part of a new connection reusing the same socket pair (IPs and ports).

## 2. Application Layer
The application layer is the top layer in both OSI and TCP/IP models. It provides the interface between applications and the network. It defines protocols that applications use to exchange data with each other.

### 2.1. WWW (World Wide Web) and HTTP (Hypertext Transfer Protocol)

*   **WWW:** A global information system where documents and other web resources are identified by Uniform Resource Locators (URLs), interlinked by hypertext links, and accessible via the Internet.
*   **HTTP:** The underlying protocol used by the WWW for communication between web browsers (clients) and web servers.
    *   **Client-Server Model:** Client (browser) sends HTTP requests to a server; server responds with HTTP responses (e.g., HTML pages, images).
    *   **Stateless:** Each request/response pair is independent by default. Server doesn't retain information about past client requests (cookies and sessions are used to add state).
    *   **Uses TCP as its transport protocol (typically on port 80 for HTTP, port 443 for HTTPS).**
    *   **HTTP/1.0:** Opens a new TCP connection for each request/response. Can be inefficient.
    *   **HTTP/1.1:** Introduces persistent connections (multiple requests/responses over a single TCP connection), pipelining (client sends multiple requests without waiting for each response - though server must respond in order).
    *   **HTTP/2:** Major revision. Introduces multiplexing (multiple requests/responses concurrently over a single TCP connection without head-of-line blocking), header compression (HPACK), server push.
    *   **HTTP/3:** Uses QUIC (Quick UDP Internet Connections) instead of TCP, further reducing latency and improving multiplexing.

    **HTTP Request Message Format:**
    ```
    Request-Line (e.g., GET /index.html HTTP/1.1)
    Header-Lines (e.g., Host: www.example.com, User-Agent: browser_info)
    (Blank Line)
    [Message Body (e.g., for POST requests)]
    ```
    *   **Request Methods:** `GET`, `POST`, `PUT`, `DELETE`, `HEAD`, `OPTIONS`, `CONNECT`, `TRACE`, `PATCH`.

    **HTTP Response Message Format:**
    ```
    Status-Line (e.g., HTTP/1.1 200 OK)
    Header-Lines (e.g., Content-Type: text/html, Content-Length: 1234)
    (Blank Line)
    [Message Body (e.g., HTML content, image data)]
    ```
    *   **Status Codes:** `1xx` (Informational), `2xx` (Success - e.g., 200 OK), `3xx` (Redirection - e.g., 301 Moved Permanently), `4xx` (Client Error - e.g., 404 Not Found), `5xx` (Server Error - e.g., 500 Internal Server Error).

*   **HTTPS (HTTP Secure):** HTTP over SSL/TLS. Encrypts HTTP traffic for security. Uses port 443.

### 2.2. FTP (File Transfer Protocol)
A standard network protocol used for transferring files from one host to another over a TCP-based network.
*   **Client-Server Model.**
*   **Uses two TCP connections:**
    1.  **Control Connection (Port 21 on server):**
        *   Established first. Persists for the entire session.
        *   Used to send commands (e.g., `USER`, `PASS`, `LIST`, `RETR`, `STOR`) and receive replies.
        *   Control information is ASCII text.
    2.  **Data Connection (Dynamically assigned port, typically port 20 on server for active mode):**
        *   Used for actual file transfer (data and directory listings).
        *   Opened for each file transfer and then closed.
*   **Modes for Data Connection:**
    *   **Active Mode:**
        1.  Client connects to FTP server's port 21 (control).
        2.  Client sends `PORT <client_ip, client_port>` command, telling server which port to connect back to for data.
        3.  Server initiates data connection from its port 20 to the client's specified IP and port.
        *   *Problem:* Firewalls on client side often block incoming connections.
    *   **Passive Mode (PASV):**
        1.  Client connects to FTP server's port 21 (control).
        2.  Client sends `PASV` command.
        3.  Server replies with an IP address and port number on the server side (`PASV <server_ip, server_port>`).
        4.  Client initiates data connection from an ephemeral port to the server's specified IP and port.
        *   *More firewall-friendly.*
*   **Authentication:** Usually username/password. Anonymous FTP allows access with generic credentials.
*   **File Types:** ASCII (for text files, handles line ending conversions), Binary/Image (for non-text files, byte-for-byte transfer).

### 2.3. SSH (Secure Shell)
A cryptographic network protocol for operating network services securely over an unsecured network.
*   **Provides:** Secure remote login, secure file transfer (SFTP, SCP), port forwarding (tunneling).
*   **Client-Server Model.**
*   **Uses TCP, typically on port 22.**
*   **Security Features:**
    *   **Strong Authentication:** Password, public-key cryptography, Kerberos.
    *   **Data Encryption:** Encrypts all traffic (including passwords) between client and server, preventing eavesdropping.
    *   **Data Integrity:** Ensures data is not modified in transit.
*   **Architecture (conceptual layers):**
    1.  **Transport Layer Protocol (SSH-TRANS):** Provides server authentication, confidentiality, integrity. Runs over TCP.
    2.  **User Authentication Protocol (SSH-AUTH):** Authenticates the client to the server.
    3.  **Connection Protocol (SSH-CONN):** Multiplexes the encrypted tunnel into several logical channels (e.g., interactive shell, forwarded X11 connections, forwarded TCP ports).
*   **Common Uses:**
    *   Logging into remote servers to execute commands.
    *   Securely transferring files using SFTP (SSH File Transfer Protocol) or SCP (Secure Copy Protocol).
    *   Tunneling other protocols (e.g., VNC, HTTP) to secure them.

### 2.4. DHCP (Dynamic Host Configuration Protocol)
A network management protocol used on IP networks whereby a DHCP server dynamically assigns an IP address and other network configuration parameters to each device (DHCP client) on a network so they can communicate with other IP networks.
*   **Client-Server Model.**
*   **Uses UDP (Client port 68, Server port 67).** Client broadcasts requests, server can unicast or broadcast replies.
*   **Goal:** Automate IP address assignment and network configuration.
*   **Information Provided by DHCP Server:**
    *   IP Address
    *   Subnet Mask
    *   Default Gateway (Router) IP Address
    *   DNS Server IP Address(es)
    *   Lease Time (duration for which the IP address is valid)
    *   Other options (e.g., NTP server, TFTP server).
*   **DHCP Process (DORA):**
    1.  **Discover (Client -> Server Broadcast):**
        *   Client (with no IP) broadcasts a `DHCPDISCOVER` message to find DHCP servers.
        *   Source IP: `0.0.0.0`, Dest IP: `255.255.255.255`.
    2.  **Offer (Server -> Client Broadcast/Unicast):**
        *   DHCP server(s) that receive the discover message may respond with a `DHCPOFFER` message, offering an IP address and other parameters.
        *   Offer includes proposed IP, lease time, server IP.
        *   Typically broadcast unless client has an IP or can receive unicast (based on flags).
    3.  **Request (Client -> Server Broadcast):**
        *   Client chooses one offer (usually the first one received) and broadcasts a `DHCPREQUEST` message, requesting the offered parameters from the chosen server.
        *   Includes the chosen server's identifier and the requested IP.
        *   Broadcast so other offering servers know their offer was declined.
    4.  **Acknowledge (Server -> Client Broadcast/Unicast):**
        *   The chosen DHCP server sends a `DHCPACK` message to confirm the IP address lease and provide any remaining configuration parameters.
        *   If the requested IP is no longer available, server sends `DHCPNAK` (Negative Acknowledgement).
*   **Lease Renewal:** Clients try to renew their lease before it expires, typically at T1 (50% of lease time) and T2 (87.5% of lease time) via unicast `DHCPREQUEST` to the leasing server.
*   **DHCP Relay Agent:** If client and server are on different subnets, a router can act as a DHCP relay agent to forward DHCP messages between them.

### 2.5. Email (SMTP, MIME, IMAP, POP3)

**Core Components:**
*   **Mail User Agent (MUA):** Client application used to read, compose, and send email (e.g., Outlook, Thunderbird, Gmail web interface).
*   **Mail Transfer Agent (MTA):** Server software that transfers email between mail servers (e.g., Sendmail, Postfix, Exchange Server).
*   **Mail Access Agent (MAA) / Mail Delivery Agent (MDA):** Server software that allows MUAs to retrieve emails from a mailbox on a server (e.g., Dovecot, Cyrus IMAPd).

**Protocols:**

**1. SMTP (Simple Mail Transfer Protocol):**
*   **Purpose:** Used for **sending** email from an MUA to an MTA, and for transferring email **between MTAs**.
*   **Uses TCP, typically on port 25.** (Port 587 for message submission with authentication, port 465 for SMTPS - SMTP over SSL/TLS - deprecated by some but still in use).
*   **Text-based protocol:** Commands and responses are ASCII text.
*   **Push Protocol:** Client (sender) pushes email to the server.
*   **Connection:**
    1.  Client establishes TCP connection to server port 25.
    2.  Handshake: `HELO`/`EHLO` (Extended HELO) from client, server responds.
    3.  Mail Transfer Commands:
        *   `MAIL FROM:<sender@example.com>`
        *   `RCPT TO:<recipient@example.com>` (can have multiple RCPT TO for multiple recipients)
        *   `DATA` (followed by email headers and body, terminated by a line with only a `.`)
    4.  Server responds with status codes (e.g., `250 OK`).
    5.  `QUIT` to close connection.
*   **Limitations:**
    *   Originally only handled 7-bit ASCII text.
    *   No inherent authentication for senders (leading to spam issues - SPF, DKIM, DMARC are used to address this).
    *   Does not provide mailbox access for users to retrieve mail.

**2. MIME (Multipurpose Internet Mail Extensions):**
*   **Purpose:** Extends the format of email messages to support:
    *   Text in character sets other than ASCII (e.g., UTF-8).
    *   Non-text attachments (e.g., images, audio, video, applications).
    *   Messages with multiple parts (e.g., HTML email and plain text alternative).
    *   Header information in non-ASCII character sets.
*   **Not a transfer protocol itself.** It defines formats for message headers and body parts.
*   **Key MIME Headers:**
    *   `MIME-Version: 1.0`
    *   `Content-Type:` (e.g., `text/plain`, `text/html`, `image/jpeg`, `application/pdf`, `multipart/mixed`)
    *   `Content-Transfer-Encoding:` (e.g., `7bit`, `8bit`, `binary`, `quoted-printable`, `base64`) - How binary data is encoded into ASCII for safe transfer over SMTP.

**3. Mail Access Protocols (for retrieving email):**

    **a. POP3 (Post Office Protocol version 3):**
    *   **Purpose:** Allows an MUA to retrieve emails from a mailbox on a server.
    *   **Uses TCP, typically on port 110.** (Port 995 for POP3S - POP3 over SSL/TLS).
    *   **Simple, limited functionality.**
    *   **Typical Mode of Operation ("Download and Delete"):**
        1.  Client connects to server.
        2.  Authentication (`USER`, `PASS`).
        3.  Transaction state: Client lists (`LIST`), retrieves (`RETR`), and marks for deletion (`DELE`) messages.
        4.  Update state: Server deletes marked messages when client issues `QUIT`.
    *   Emails are typically downloaded to the client device and removed from the server (though a "keep on server" option exists).
    *   Not ideal for accessing email from multiple devices as messages are not synchronized.

    **b. IMAP (Internet Message Access Protocol):**
    *   **Purpose:** Allows an MUA to access and manage emails on a remote server.
    *   **Uses TCP, typically on port 143.** (Port 993 for IMAPS - IMAP over SSL/TLS).
    *   **More complex and feature-rich than POP3.**
    *   **Key Features:**
        *   **Emails remain on the server by default.** Client manipulates remote messages.
        *   **Multiple mailboxes (folders):** Supports creating, deleting, renaming folders on the server.
        *   **Message state flags:** Tracks message status (e.g., seen, answered, flagged) on the server, synchronized across clients.
        *   **Partial fetch:** Can download parts of messages (e.g., headers only, or a specific MIME part) - useful for slow connections.
        *   **Server-side searching.**
        *   **Online, Offline, and Disconnected Modes:** Allows for more flexible access.
    *   Well-suited for accessing email from multiple devices and for webmail interfaces, as the mail store is centralized and synchronized.

### 2.6. DNS (Domain Name System)
A hierarchical and distributed naming system for computers, services, or any resource connected to the Internet or a private network. It translates human-readable domain names (e.g., `www.google.com`) into machine-readable IP addresses (e.g., `172.217.160.142`).
*   **Uses UDP for most queries (port 53) for speed.** Can use TCP (port 53) for large responses (e.g., zone transfers) or when reliability is critical.
*   **Hierarchical Structure:**
    *   **Root Level Domain (.)**
    *   **Top-Level Domains (TLDs):** e.g., `.com`, `.org`, `.net`, `.gov`, country codes like `.uk`, `.in`.
    *   **Second-Level Domains (SLDs):** e.g., `google.com`, `example.org`.
    *   **Subdomains:** e.g., `mail.google.com`, `docs.example.org`.
*   **DNS Servers:**
    *   **Root Servers:** 13 logical servers (many physical instances worldwide) that know addresses of TLD servers.
    *   **TLD Servers:** Manage address information for their specific TLD (e.g., `.com` TLD server knows authoritative servers for `google.com`).
    *   **Authoritative Servers:** Responsible for providing answers for domain names within their specific zone(s) (e.g., Google's DNS servers are authoritative for `google.com`).
    *   **Recursive Resolvers (Caching Name Servers):** Typically provided by ISPs or organizations. Clients send queries to these resolvers. The resolver performs the iterative queries to root, TLD, and authoritative servers on behalf of the client. It also caches results to speed up future queries.
*   **DNS Resolution Process (Simplified):**
    1.  User types `www.example.com` in browser.
    2.  Client OS queries its configured Recursive Resolver (e.g., ISP's DNS).
    3.  **Recursive Resolver (if not cached):**
        a.  Queries a Root Server for `.` for `www.example.com`.
        b.  Root Server responds with address of `.com` TLD Server.
        c.  Resolver queries `.com` TLD Server for `www.example.com`.
        d.  `.com` TLD Server responds with address of Authoritative Server for `example.com`.
        e.  Resolver queries Authoritative Server for `example.com` for `www.example.com`.
        f.  Authoritative Server responds with the IP address of `www.example.com`.
    4.  Recursive Resolver sends IP address back to client OS.
    5.  Client OS gives IP to browser; browser connects to the IP address.
*   **DNS Record Types:**
    *   `A`: Host address (IPv4).
    *   `AAAA`: IPv6 host address.
    *   `CNAME`: Canonical Name (alias for another domain name).
    *   `MX`: Mail Exchange (specifies mail server for a domain).
    *   `NS`: Name Server (delegates a zone to an authoritative server).
    *   `PTR`: Pointer (reverse DNS lookup - IP to domain name).
    *   `SOA`: Start of Authority (administrative info about a zone).
    *   `TXT`: Text records (e.g., for SPF, DKIM, domain verification).
*   **Caching:** DNS responses are cached at various levels (client OS, recursive resolvers) with a Time-To-Live (TTL) value to reduce load and latency.

### 2.7. Network Management (SNMP - Simple Network Management Protocol)
An Internet Standard protocol for collecting and organizing information about managed devices on IP networks and for modifying that information to change device behavior.
*   **Components:**
    1.  **Managed Devices:** Network nodes (e.g., routers, switches, servers, printers) that contain SNMP agents.
    2.  **SNMP Agent:** Software module residing on a managed device. It collects and stores management information in a local database (MIB) and responds to SNMP manager requests.
    3.  **SNMP Manager (or Network Management Station - NMS):** Application that runs on a management host. It queries agents, gets responses, sets variables in agents, and acknowledges asynchronous events (traps) from agents.
    4.  **Management Information Base (MIB):** A hierarchical database of managed objects on a device. Each object represents a piece of information (e.g., CPU load, interface status, error counts). Objects are identified by Object Identifiers (OIDs).
*   **Uses UDP (Agent port 161, Manager port 162 for traps).**
*   **SNMP Protocol Operations (PDUs - Protocol Data Units):**
    *   `GetRequest`: Manager requests the value of one or more MIB objects from an agent.
    *   `GetNextRequest`: Manager requests the value of the next MIB object in the MIB tree (used for traversing tables).
    *   `GetResponse`: Agent replies to GetRequest/GetNextRequest, providing values or error status.
    *   `SetRequest`: Manager requests the agent to change the value of one or more MIB objects.
    *   `Trap`: Asynchronous notification sent by an agent to a manager to report a significant event (e.g., link down, device reboot). (SNMPv1)
    *   `InformRequest`: (SNMPv2c and later) Acknowledged trap. Manager sends a response to an Inform.
*   **SNMP Versions:**
    *   **SNMPv1:** Original version. Simple, but limited security (community strings - like passwords sent in clear text).
    *   **SNMPv2c:** Introduces `GetBulkRequest` (efficiently retrieve large blocks of data), improved error handling. Still uses community strings for security.
    *   **SNMPv3:** Focuses on security. Provides:
        *   **Authentication:** Verifies the source of messages (e.g., MD5, SHA).
        *   **Encryption:** Encrypts SNMP messages (e.g., DES, AES) for privacy.
        *   **Access Control:** Defines who can access which MIB objects and what operations they can perform.
        *   User-based Security Model (USM) and View-based Access Control Model (VACM).
*   **MIB Structure:** Tree-like structure defined by SMI (Structure of Management Information). OIDs are like paths in this tree (e.g., `1.3.6.1.2.1.1.1.0` for system description).