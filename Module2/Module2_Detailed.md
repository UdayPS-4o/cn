# Module 2: Data Link Layer

## Data Link Layer: Need and Services Provided

The Data Link Layer (DLL), Layer 2 in the OSI model, is responsible for reliable data transfer between two directly connected nodes (physically or logically) in a network. It takes packets from the Network Layer and encapsulates them into **frames** for transmission. It also handles errors, flow control, and media access if the link is shared.

**Recap: Where does DLL fit?**
*   **OSI Model:** Layer 2 (between Physical and Network Layer)
*   **TCP/IP Model:** Part of the Network Access/Link Layer

**Need for the Data Link Layer:**

1.  **Transforming a Raw Transmission Facility:** The Physical Layer provides a raw bit stream service. The DLL aims to transform this into a link that appears free of undetected transmission errors to the Network Layer.
2.  **Node-to-Node Delivery (Hop-to-Hop):** While the Network Layer deals with end-to-end delivery (source to ultimate destination), the DLL is concerned with moving a frame from one node to the *next* node in the path (e.g., computer to switch, switch to router, router to router).
3.  **Dealing with Physical Layer Limitations:** Physical media are not perfect. They can introduce errors, have limited data rates, and propagation delays. DLL protocols are designed to manage these issues for a single link.
4.  **Providing an Interface to the Network Layer:** It offers a well-defined service interface to the Network Layer, hiding the complexities of the underlying hardware and link characteristics.
5.  **Managing Shared Access:** In broadcast networks (like Ethernet or Wi-Fi), where multiple devices share the same physical medium, the DLL (specifically its MAC sublayer) is needed to coordinate access and prevent collisions.

**Services Provided by the Data Link Layer:**

The specific services can vary depending on the protocol, but generally include:

1.  **Framing:**
    *   **Function:** The DLL divides the stream of bits received from the Network Layer into manageable data units called **frames**.
    *   **Process:** It adds a header and a trailer to the network layer packet (datagram) to create a frame.
        *   **Header:** Contains control information like source and destination physical addresses (MAC addresses), frame type, and control fields for flow/error control.
        *   **Trailer:** Often contains error detection codes (e.g., Frame Check Sequence - FCS).
    *   **Importance:** Clearly defines the beginning and end of a block of data, allowing the receiver to identify where a frame starts and ends in the bit stream.

2.  **Physical Addressing (MAC Addressing):**
    *   **Function:** If frames are to be distributed to different systems on a shared network (like a LAN), the DLL adds a header containing the physical addresses of the sender and/or receiver.
    *   **MAC Address:** A unique hardware address (usually 48 bits or 6 bytes) burned into the Network Interface Card (NIC) of a device.
    *   **Scope:** Used for local delivery within the same network segment. Routers will strip DLL headers and create new ones when forwarding packets between different networks.

3.  **Flow Control:**
    *   **Function:** Prevents a fast sender from overwhelming a slow receiver with too much data too quickly.
    *   **Mechanism:** The receiver sends feedback to the sender to regulate the transmission rate. If the receiver's buffers are getting full, it can instruct the sender to pause or slow down.
    *   **Importance:** Ensures efficient data transfer without data loss due to buffer overflows at the receiver.
    *   **Techniques:** Stop-and-Wait, Sliding Window protocols.

4.  **Error Control:**
    *   **Function:** Detects and, in some cases, corrects errors that occur during transmission over the physical medium (due to noise, interference, etc.).
    *   **Mechanisms:**
        *   **Error Detection:** Adds redundant bits (e.g., Parity Check, Cyclic Redundancy Check - CRC) to the frame, which are then checked by the receiver. If an error is detected, the frame is usually discarded.
        *   **Error Correction:** Uses more sophisticated codes (e.g., Hamming codes) that allow the receiver to not only detect but also correct certain types of errors. This is less common in DLL for wired networks but used in wireless.
        *   **Retransmission:** If a frame is lost or detected as corrupted, protocols often rely on acknowledgments (ACKs) and timeouts to trigger retransmission of the affected frame(s). This is a key part of reliable DLL services.
    *   **Scope:** Usually performed on a hop-to-hop basis.

5.  **Access Control (Media Access Control - MAC):**
    *   **Function:** When multiple devices share the same communication channel (e.g., in a bus topology LAN or a wireless network), the MAC sublayer of the DLL determines which device has the right to transmit at any given time.
    *   **Importance:** Prevents or manages collisions that occur when multiple devices transmit simultaneously.
    *   **Techniques:**
        *   **Contention-based:** ALOHA, CSMA, CSMA/CD, CSMA/CA.
        *   **Controlled access:** Token Passing, Polling.

6.  **Synchronization:**
    *   **Function:** Ensures that the sender and receiver are synchronized for frame reception. This includes identifying frame boundaries.
    *   **Mechanism:** Framing methods themselves help with this (e.g., using special bit patterns or character counts).

7.  **Sequencing (for connection-oriented services):**
    *   **Function:** If frames can arrive out of order (e.g., over parallel paths, though less common at L2 directly), sequence numbers can be used to reorder them correctly at the destination DLL. More typically, this is a higher-layer function, but some DLL protocols might ensure ordered delivery over a single link.

**Types of Services Offered to the Network Layer:**

The DLL can offer different types of services to the Network Layer:

1.  **Unacknowledged Connectionless Service:**
    *   Sender sends frames independently without any prior connection setup.
    *   Receiver does not acknowledge received frames.
    *   No error control or flow control at the DLL beyond basic error detection (discarding bad frames).
    *   **Use Case:** Low error rate links, real-time traffic where retransmission delays are unacceptable (higher layers might handle errors). Ethernet is a primary example.
    *   **Analogy:** Sending a postcard – no guarantee of delivery, no notification if lost.

2.  **Acknowledged Connectionless Service:**
    *   No prior connection setup.
    *   Each frame sent is individually acknowledged by the receiver.
    *   If an ACK is not received within a timeout, the sender retransmits the frame.
    *   **Use Case:** Unreliable channels like wireless networks (e.g., IEEE 802.11 Wi-Fi uses acknowledgments).
    *   **Analogy:** Sending a registered letter where you get a confirmation of receipt for each letter.

3.  **Acknowledged Connection-Oriented Service:**
    *   A logical connection is established between the sender and receiver before data transfer.
    *   Frames are numbered and acknowledged. Guarantees that all frames are received, and in the correct order.
    *   Includes flow control and error control.
    *   **Phases:** Connection setup, data transfer, connection release.
    *   **Use Case:** Point-to-point links where high reliability is paramount. Some older WAN protocols or specialized links.
    *   **Analogy:** A phone call – you establish a connection, exchange information with confirmation, and then hang up.

Most LANs (like Ethernet) use unacknowledged connectionless service for efficiency, relying on higher-layer protocols (like TCP) for overall reliability if needed. Wireless LANs (like Wi-Fi) often use acknowledged connectionless service due to the higher error rates of the wireless medium.

## Framing

Framing is the process of partitioning a stream of bits from the Network Layer into manageable units called frames. Each frame has a defined start and end, allowing the receiver's DLL to detect frame boundaries.

**Why is Framing Necessary?**

*   **Error Control:** Error detection/correction is typically done on a per-frame basis.
*   **Flow Control:** Managing data flow based on frame units.
*   **Identifying Data:** Distinguishing control information (headers/trailers) from the actual payload data.
*   **Addressing:** MAC addresses are part of the frame header.

**Framing Methods:**

1.  **Character Count (or Byte Count):**
    *   **Idea:** The header of the frame contains a field specifying the total number of characters (or bytes) in the frame, including the count field itself.
    *   **Process:** The receiver reads the count value and then reads that many subsequent characters/bytes to extract the frame.
    *   **Problem:** If the count field gets corrupted by a transmission error, the receiver loses synchronization and cannot correctly locate the end of the current frame or the start of the next. This is known as **resynchronization problem**.
    *   **Example:** If count says 100, but it was actually 10 and got corrupted, receiver reads 90 bytes into the next frame.
    *   **Usage:** Rarely used in modern protocols due to its unreliability.

2.  **Character Stuffing (or Byte Stuffing):**
    *   **Idea:** Uses special "flag" characters (or bytes) to mark the beginning and end of a frame. A common flag is `DLE STX` (Data Link Escape, Start of TeXt) for the beginning and `DLE ETX` (Data Link Escape, End of TeXt) for the end.
    *   **Problem:** What if the flag character pattern appears in the actual data? The receiver would mistakenly interpret it as the end of the frame.
    *   **Solution (Character Stuffing):**
        *   The sender's DLL scans the data for any occurrence of the DLE character.
        *   If a DLE character is found in the data, the sender inserts an extra DLE character immediately after it.
        *   The receiver's DLL, when it encounters two consecutive DLE characters in the data stream, removes the second one (destuffs it).
        *   If `DLE ETX` is received, it's the end of the frame. If `DLE` followed by something other than `DLE` or `ETX` (or `STX`) occurs, it's an error.
    *   **Example:**
        *   Flag: `F` (single character for simplicity)
        *   Data: `A F B`
        *   Sender stuffs an escape character `ESC` before `F` in data: `A ESC F B`
        *   Transmitted Frame: `F A ESC F B F`
        *   Receiver sees `ESC F`, removes `ESC`, gets `A F B`.
    *   **Protocols:** PPP (Point-to-Point Protocol) uses a similar technique with a flag byte `0x7E`. If `0x7E` appears in data, it's replaced by `0x7D, 0x5E`. If `0x7D` appears in data, it's replaced by `0x7D, 0x5D`. This is byte stuffing.
    *   **Advantages:** Handles data transparency well.
    *   **Disadvantages:** Can increase the size of the frame due to stuffed characters. Tied to character codes (e.g., 8-bit characters).

3.  **Bit Stuffing:**
    *   **Idea:** Similar to character stuffing, but operates at the bit level. Allows arbitrary data content (not just characters).
    *   **Process:**
        *   A special bit pattern, called a **flag**, typically `01111110` (six consecutive 1s flanked by 0s), marks the beginning and end of a frame.
        *   **Sender:** To ensure the flag pattern never accidentally appears in the data, the sender's DLL monitors the data bit stream. If it encounters five consecutive 1s in the data, it automatically inserts (stuffs) a `0` bit immediately after the fifth 1.
        *   **Receiver:** When the receiver sees five consecutive 1s followed by a `0` bit, it automatically removes (destuffs) the `0` bit.
        *   If the receiver sees five consecutive 1s followed by a `1` bit, it checks the next bit:
            *   If it's `0` (`01111110`), it's a flag (start/end of frame).
            *   If it's `1` (`01111111...`), it indicates an error or an abort sequence.
    *   **Example:**
        *   Flag: `01111110`
        *   Data: `...011111110111110...`
        *   Sender sees `011111` (five 1s), stuffs a `0`: `...0111110101111100...`
        *   Transmitted Frame: `01111110 ...0111110101111100... 01111110`
        *   Receiver sees `0111110`, destuffs the `0`.
    *   **Protocols:** HDLC (High-Level Data Link Control), SDLC (Synchronous Data Link Control), USB.
    *   **Advantages:** Data transparency (any bit pattern can be sent), efficient.
    *   **Disadvantages:** A single bit error can corrupt the stuffing/destuffing process.

4.  **Physical Layer Coding Violations:**
    *   **Idea:** Some physical layer encoding schemes (e.g., Manchester encoding used in older Ethernets) have inherent redundancy. For example, in Manchester encoding, each bit period has a transition in the middle. A lack of transition or a specific invalid pattern can be used to signal frame boundaries.
    *   **Process:** The frame boundary is indicated by a symbol or signal pattern that is not a valid data symbol in the encoding scheme.
    *   **Example:** In 10 Mbps Ethernet using Manchester encoding, the start and end of a frame are sometimes delimited by specific non-data symbols (though Ethernet predominantly uses a preamble and SFD - Start Frame Delimiter - for synchronization and start detection).
    *   **Advantages:** No overhead in terms of extra bits/bytes in the data stream itself.
    *   **Disadvantages:** Highly dependent on the specific physical layer encoding used. Not universally applicable.

**Ethernet Framing (IEEE 802.3):**

Ethernet uses a slightly different approach that doesn't rely on stuffing:

*   **Preamble (7 bytes):** A pattern of alternating `10101010` bytes. Used by the receiver to synchronize its clock with the sender's clock.
*   **Start Frame Delimiter (SFD) (1 byte):** `10101011`. Signals the end of the preamble and the beginning of the actual frame content.
*   **Destination MAC Address (6 bytes):** Physical address of the destination NIC.
*   **Source MAC Address (6 bytes):** Physical address of the sending NIC.
*   **Length/Type (2 bytes):**
    *   If value <= 1500 (0x05DC): Indicates the length of the data field in bytes. (Used in original IEEE 802.3)
    *   If value >= 1536 (0x0600): Indicates the EtherType of the encapsulated protocol (e.g., 0x0800 for IPv4, 0x0806 for ARP). (Used in Ethernet II / DIX frames, which is dominant today).
*   **Data (Payload) (46-1500 bytes):** The actual data from the upper layer (e.g., IP packet). If less than 46 bytes, padding is added to meet the minimum frame size.
*   **Frame Check Sequence (FCS) (4 bytes):** A 32-bit CRC value calculated over the Destination MAC, Source MAC, Length/Type, and Data fields. Used for error detection.

Ethernet relies on the inter-frame gap (a minimum idle time on the medium between frames) and the SFD to determine frame boundaries rather than flags and stuffing.

## Flow Control

Flow control is a mechanism to regulate the rate of data transmission between two nodes to prevent a fast sender from overwhelming a slow receiver. If the sender transmits data faster than the receiver can process and buffer it, data will be lost.

**Why is Flow Control Needed?**

*   **Limited Buffer Capacity:** Receivers have finite buffer space to store incoming frames before they are processed.
*   **Varying Processing Speeds:** Sender and receiver might have different processing capabilities or be subject to varying loads.
*   **Preventing Data Loss:** Ensures data is not dropped due to buffer overflow.

**Flow Control Mechanisms (primarily for connection-oriented DLL services, or when acknowledgments are used):**

These mechanisms are often intertwined with error control.

1.  **Stop-and-Wait Protocol:**
    *   **Simplest form of flow control (and error control).**
    *   **Sender:**
        1.  Sends one frame.
        2.  Waits for an acknowledgment (ACK) from the receiver before sending the next frame.
    *   **Receiver:**
        1.  Receives the frame.
        2.  If the frame is correct, processes it and sends an ACK back to the sender.
    *   **Working:**
        *   If ACK is received, sender transmits the next frame.
        *   If ACK is not received within a certain timeout period (e.g., due to frame loss, ACK loss, or frame corruption), the sender retransmits the current frame.
        *   Sequence numbers (e.g., 0 and 1) are used for frames and ACKs to handle duplicate frames or ACKs if an ACK is delayed but not lost.
    *   **Efficiency:** Very inefficient, especially on links with long propagation delays or high data rates. The sender spends a lot of time waiting for ACKs.
        *   **Link Utilization (U):** `U = (T_frame) / (T_frame + 2 * T_prop)` where `T_frame` is frame transmission time, `T_prop` is propagation delay. If `2 * T_prop` is large compared to `T_frame`, utilization is low.
    *   **Advantages:** Simple to implement.
    *   **Disadvantages:** Low throughput, inefficient use of the channel.

2.  **Sliding Window Protocols:**
    *   **Idea:** Allows the sender to transmit multiple frames (`W` frames, where `W` is the window size) before needing to wait for an acknowledgment for the first frame. This keeps the communication pipe fuller, improving efficiency.
    *   **Key Concepts:**
        *   **Sender Window (SWS):** A range of sequence numbers of frames that the sender is allowed to transmit without receiving an ACK. The window "slides" forward as ACKs are received for earlier frames.
        *   **Receiver Window (RWS):** A range of sequence numbers of frames that the receiver is prepared to accept.
        *   **Sequence Numbers:** Frames are numbered modulo `N` (e.g., if 3 bits are used for sequence numbers, `N = 2^3 = 8`, so sequence numbers are 0-7).
        *   **Acknowledgments (ACKs):** The receiver sends ACKs to confirm receipt of frames. ACKs can be cumulative (acknowledging all frames up to a certain number) or selective.
        *   **Timeouts:** Used to detect lost frames or lost ACKs.
    *   **General Operation:**
        1.  Sender can transmit up to SWS frames without waiting.
        2.  As frames are transmitted, the sender's window shrinks from the left (oldest unacknowledged frame) and potentially expands from the right (new frames allowed to be sent) as ACKs arrive.
        3.  Receiver maintains a window of acceptable frames. When a frame arrives within its window and is correct, it's passed to the network layer (if in order) and an ACK is sent.
        4.  The receiver's window slides forward.
    *   **Efficiency:** Much higher than Stop-and-Wait, as it allows pipelining of frames.
        *   Max window size for `m`-bit sequence numbers: `SWS <= 2^m - 1` for Go-Back-N, `SWS + RWS <= 2^m` for Selective Repeat.
    *   **Types of Sliding Window Protocols:**
        *   **Go-Back-N ARQ**
        *   **Selective Repeat ARQ**

**Relationship to Error Control:** Flow control mechanisms, especially sliding window protocols, are tightly integrated with ARQ (Automatic Repeat reQuest) error control strategies.

## Error Control

Error control mechanisms are used to detect and/or correct errors that occur during the transmission of frames over a physical link. Transmission errors are caused by noise, interference, or distortion.

**Types of Errors:**

1.  **Single-Bit Error:** Only one bit in the data unit (e.g., frame) is changed from 1 to 0 or 0 to 1. Least likely type of error in serial transmission because noise often affects multiple bits.
2.  **Burst Error:** Two or more bits in the data unit are changed. The length of the burst is measured from the first corrupted bit to the last corrupted bit (inclusive). Burst errors are more common.

**Error Control Techniques:**

1.  **Error Detection:**
    *   Adds redundant information (check bits) to the data by the sender.
    *   The receiver performs calculations on the received data (including check bits) and compares the result with the received check bits or an expected value.
    *   If a mismatch occurs, an error is detected.
    *   Detected erroneous frames are typically discarded. Retransmission is then handled by ARQ mechanisms.
    *   **Common Error Detection Codes:**
        *   **Parity Check (Single Parity Bit):**
            *   **Idea:** An extra bit (parity bit) is appended to a block of data bits such_that the total number of 1s (including the parity bit) is either even (even parity) or odd (odd parity).
            *   **Example (Even Parity):**
                *   Data: `1011001` (four 1s) -> Parity bit: `0` -> Transmitted: `10110010`
                *   Data: `1010001` (three 1s) -> Parity bit: `1` -> Transmitted: `10100011`
            *   **Detection:** The receiver counts the 1s in the received data (including the parity bit). If the count doesn't match the agreed-upon parity (even/odd), an error is detected.
            *   **Capability:** Can detect all single-bit errors. Can detect burst errors only if they flip an odd number of bits. Cannot detect burst errors that flip an even number of bits.
            *   **Usage:** Simple, but not very robust. Used in older systems or for very basic checks.
            *   **Two-Dimensional Parity (Block Sum Check / Longitudinal Redundancy Check - LRC):** Arranges data in a table and calculates parity for each row and each column. The column parities form an extra "parity byte." More robust than single parity.
        *   **Checksum:**
            *   **Idea:** The data is treated as a sequence of integers (e.g., 16-bit words). These integers are summed up (often using one's complement arithmetic). The (complemented) sum is appended as the checksum.
            *   **Sender:**
                1.  Divide data into `k`-bit segments.
                2.  Sum all segments using one's complement arithmetic.
                3.  Take the one's complement of the sum – this is the checksum.
                4.  Append checksum to the data.
            *   **Receiver:**
                1.  Divide received data (including checksum) into `k`-bit segments.
                2.  Sum all segments (including the checksum) using one's complement.
                3.  If the result is all 1s (or all 0s, depending on the exact method), no error is detected. Otherwise, an error occurred.
            *   **Usage:** Used in some Internet protocols (e.g., IP, TCP, UDP headers have checksums, though often implemented in software at higher layers).
            *   **Capability:** Better than parity but can still miss some error patterns.
        *   **Cyclic Redundancy Check (CRC):**
            *   **Most powerful and commonly used error detection method in DLL.**
            *   **Idea:** Based on binary polynomial division.
            *   **Sender:**
                1.  Treat the `k`-bit data block as a polynomial `M(x)`.
                2.  Append `r` zero bits to the data block, where `r` is the degree of a pre-defined **generator polynomial** `G(x)` (e.g., CRC-16 means `r=16`, `G(x)` has degree 16). This new `(k+r)`-bit block corresponds to `x^r * M(x)`.
                3.  Divide `x^r * M(x)` by `G(x)` using modulo-2 polynomial division (XOR for subtraction/addition).
                4.  The `r`-bit remainder `R(x)` is the CRC checksum or Frame Check Sequence (FCS).
                5.  Transmit the original `k` data bits followed by the `r` CRC bits: `T(x) = x^r * M(x) + R(x)`. This transmitted frame `T(x)` is exactly divisible by `G(x)`.
            *   **Receiver:**
                1.  Receives the `(k+r)`-bit frame.
                2.  Divides the received frame by the same generator polynomial `G(x)`.
                3.  If the remainder is zero, no error is detected.
                4.  If the remainder is non-zero, an error is detected, and the frame is discarded.
            *   **Generator Polynomials:** Standard generator polynomials exist (e.g., CRC-12, CRC-16, CRC-32, CRC-CCITT). The choice of `G(x)` determines the error detection capabilities.
            *   **Capability:**
                *   Can detect all single-bit errors.
                *   Can detect all double-bit errors (if `G(x)` has at least three 1s).
                *   Can detect any odd number of errors (if `G(x)` contains `(x+1)` as a factor).
                *   Can detect all burst errors of length `<= r`.
                *   Can detect most burst errors of length `> r` with high probability.
            *   **Usage:** Ethernet, Wi-Fi, HDLC, ATM, many storage devices. Hardware implementation is efficient.

2.  **Error Correction:**
    *   Requires more redundant bits than error detection.
    *   Allows the receiver to identify the location of the error(s) and correct them without needing retransmission.
    *   **Forward Error Correction (FEC):**
        *   **Hamming Codes:**
            *   A popular FEC code. Can detect up to two-bit errors or correct single-bit errors.
            *   The number of parity bits `r` required for `m` data bits is determined by `(m + r + 1) <= 2^r`.
            *   Parity bits are placed at positions that are powers of 2 (1, 2, 4, 8,...). Each parity bit checks specific data bit positions.
            *   Receiver calculates parity checks. The pattern of erroneous checks (syndrome) indicates the position of the error, which can then be flipped.
        *   **Other FEC Codes:** Reed-Solomon codes (good for burst errors), LDPC codes, Turbo codes.
    *   **Usage:**
        *   More common in situations where retransmissions are costly or impossible (e.g., deep space communication, wireless links with high error rates and long delays, data storage like CDs/DVDs/SSDs).
        *   Less common for wired Data Link Layers like Ethernet, which prefer simpler error detection and retransmission via ARQ because links are generally reliable.

**Automatic Repeat reQuest (ARQ) Mechanisms:**
ARQ combines error detection with retransmission requests. When the receiver detects an error in a frame, it discards the frame. The sender retransmits the frame when it doesn't receive an ACK (or receives a NAK - Negative Acknowledgment).

*   **Stop-and-Wait ARQ** (already discussed under flow control)
*   **Go-Back-N ARQ** (discussed in Sliding Window section)
*   **Selective Repeat ARQ** (discussed in Sliding Window section)

## Data Link Layer Protocol: Elementary & Sliding Window Protocols

These protocols combine framing, flow control, and error control.

### Elementary Data Link Protocols (Conceptual Building Blocks)

These are simplified protocols, often used to illustrate basic concepts.

1.  **Utopian Simplex Protocol (No Error/No Flow Control):**
    *   Assumptions: Sender always has data, receiver always ready, error-free channel, unidirectional data flow.
    *   Sender: Sends frames continuously.
    *   Receiver: Receives frames continuously.
    *   Unrealistic but a starting point.

2.  **Simplex Stop-and-Wait Protocol (Error-Free, but with Flow Control):**
    *   Assumptions: Error-free channel, receiver has finite buffer/processing speed.
    *   Sender: Sends one frame, then waits for a "dummy" acknowledgment (permission to send next) from the receiver.
    *   Receiver: Receives frame, processes it, sends permission.
    *   Introduces basic flow control.

3.  **Simplex Protocol for a Noisy Channel (Stop-and-Wait ARQ):**
    *   This is the classic Stop-and-Wait ARQ discussed earlier.
    *   Handles:
        *   Lost frames (detected by sender timeout).
        *   Lost ACKs (detected by sender timeout, leading to duplicate frame).
        *   Corrupted frames (detected by receiver CRC, frame discarded, sender times out).
    *   Requires sequence numbers (e.g., 0 and 1 for frames and ACKs) to distinguish between original and retransmitted frames/ACKs.
        *   Sender sends Frame 0.
        *   Receiver gets Frame 0, sends ACK 0.
        *   Sender gets ACK 0, sends Frame 1.
        *   If Frame 0 was lost, sender times out, resends Frame 0.
        *   If ACK 0 was lost, sender times out, resends Frame 0. Receiver gets duplicate Frame 0, recognizes it (expects Frame 1), discards it, but resends ACK 0.

### Sliding Window Protocols

These are more practical and efficient, allowing multiple frames to be "in flight."

**Key Parameters:**
*   `m`: Number of bits for sequence numbers. Sequence numbers range from `0` to `2^m - 1`.
*   `SWS`: Sender Window Size.
*   `RWS`: Receiver Window Size.

**1. One-Bit Sliding Window Protocol:**
    *   This is essentially Stop-and-Wait ARQ.
    *   Sequence numbers are 0 and 1 (`m=1`).
    *   `SWS = 1`, `RWS = 1`.
    *   Sender transmits a frame (seq 0). Waits for ACK 0.
    *   Receiver expects frame 0. If received correctly, sends ACK 0, expects frame 1.
    *   Sender receives ACK 0, transmits frame 1. Waits for ACK 1.
    *   And so on.

**2. Go-Back-N ARQ:**
    *   **Sender Window Size (SWS):** Can be up to `2^m - 1`.
    *   **Receiver Window Size (RWS):** Always `1`.
    *   **Sender Behavior:**
        *   Can send up to SWS frames without waiting for ACKs.
        *   Maintains a timer for the oldest unacknowledged frame.
        *   If a frame is lost or corrupted, or its ACK is lost:
            *   The receiver will only accept frames in order. If frame `i` is lost, receiver discards `i+1, i+2, ...` even if they arrive correctly, until `i` is received.
            *   When the sender's timer for frame `i` expires (or it receives a NAK for frame `i`, if used), it **goes back** and retransmits frame `i` and all subsequent frames that were already sent (i.e., frames `i, i+1, ..., i+SWS-1` that were in the window).
    *   **Receiver Behavior:**
        *   Only accepts frames in the correct sequence number order.
        *   If frame `i` is received correctly and `i` is the expected sequence number, it's passed to the Network Layer, and an ACK for `i` (often cumulative, meaning `ACK (i+1)` as "expecting `i+1`") is sent. The expected sequence number is incremented.
        *   If an out-of-order frame `j` (where `j != expected_seq_num`) is received, it is discarded. The receiver might resend the ACK for the last correctly received in-order frame.
    *   **Acknowledgments:** Typically cumulative. `ACK n` confirms receipt of all frames up to `n-1`.
    *   **Example (`m=3`, seq nums 0-7, `SWS=7`):**
        1. Sender sends F0, F1, F2, F3, F4, F5, F6.
        2. F2 is lost.
        3. Receiver gets F0, sends ACK1 (expecting F1). Gets F1, sends ACK2 (expecting F2).
        4. Receiver gets F3 (expects F2), discards F3. Gets F4, discards F4, etc. Keeps sending ACK2.
        5. Sender's timer for F2 expires.
        6. Sender retransmits F2, F3, F4, F5, F6.
    *   **Advantages:**
        *   Simpler receiver logic (doesn't need to buffer out-of-order frames).
        *   More efficient than Stop-and-Wait.
    *   **Disadvantages:**
        *   Can be wasteful as correctly transmitted frames after a lost/corrupted one are retransmitted. This is particularly bad on links with high error rates or long delays where many frames might be in the pipeline.

**3. Selective Repeat ARQ (or Selective Reject ARQ):**
    *   **Sender Window Size (SWS):** Can be up to `2^(m-1)`.
    *   **Receiver Window Size (RWS):** Also up to `2^(m-1)`. Typically `SWS = RWS`.
    *   **Constraint:** `SWS + RWS <= 2^m`. This is crucial to avoid ambiguity between old and new window instances when sequence numbers wrap around. If `SWS = RWS`, then `SWS <= 2^(m-1)`.
    *   **Sender Behavior:**
        *   Can send up to SWS frames.
        *   Maintains a timer for each unacknowledged frame.
        *   If a timer for frame `i` expires, or a NAK for `i` is received, the sender only retransmits frame `i`. It does not retransmit subsequent correctly acknowledged frames.
    *   **Receiver Behavior:**
        *   Accepts and buffers out-of-order frames if they fall within its RWS.
        *   When frame `i` (expected or out-of-order but within RWS) is received correctly:
            *   It sends a **selective acknowledgment (SACK)** for frame `i` (or a cumulative ACK if appropriate).
            *   If frame `i` is the one it was expecting (lowest sequence number in its window), it's passed to the Network Layer along with any buffered, contiguous, previously received frames. The receiver window then slides forward.
            *   If frame `i` is out of order, it's buffered until all preceding frames are received.
    *   **Acknowledgments:**
        *   Often SACKs are used, acknowledging specific frames.
        *   Cumulative ACKs can also be used, but NAKs might be needed to signal specific missing frames.
    *   **Example (`m=3`, seq nums 0-7, `SWS=RWS=4`):**
        1. Sender sends F0, F1, F2, F3.
        2. F2 is lost.
        3. Receiver gets F0, sends ACK0 (or SACK0). Gets F1, sends ACK1 (or SACK1).
        4. Receiver gets F3. Since F2 is missing, F3 is out of order but within RWS. It buffers F3 and sends SACK3 (or NAK2).
        5. Sender's timer for F2 expires (or it receives NAK2).
        6. Sender retransmits *only* F2.
        7. Receiver gets F2. Now it has F0, F1, F2, F3. It passes them all to the Network Layer and sends ACK4 (cumulative).
    *   **Advantages:**
        *   Most efficient in terms of bandwidth utilization, as only lost/corrupted frames are retransmitted.
    *   **Disadvantages:**
        *   More complex receiver logic (requires buffering for out-of-order frames and reordering).
        *   Sender needs to manage individual timers for each frame in the window.

**Window Size Considerations:**
*   The window size `W` should ideally be large enough to keep the pipe full: `W * T_frame >= T_frame + 2 * T_prop`.
    This means `W >= 1 + (2 * T_prop / T_frame)`. This quantity `(2 * T_prop / T_frame)` is often called `a`. So `W >= 1 + a`.
*   The maximum sequence number (`2^m - 1`) must be large enough to accommodate the window size based on the protocol rules.

### Hybrid ARQ (HARQ)
*   A more advanced form of ARQ that combines Forward Error Correction (FEC) with standard ARQ.
*   **Idea:**
    1.  Data is encoded with FEC bits.
    2.  Upon reception, if the errors are within the FEC's correction capability, they are corrected, and the frame is accepted.
    3.  If errors are detected but cannot be corrected by FEC, the receiver might store the corrupted frame and request a retransmission.
    4.  The retransmitted frame might be another copy of the original, or it might contain additional parity/FEC information.
    5.  **Chase Combining:** The receiver can combine multiple (corrupted) copies of the same frame to improve the chances of successful decoding.
    6.  **Incremental Redundancy:** Subsequent retransmissions send different/additional error-correction bits rather than just repeating the original data. This gradually increases the amount of FEC information until decoding is successful.
*   **Benefits:**
    *   Improves throughput and reduces latency, especially on noisy channels (like wireless).
    *   Reduces the number of retransmissions compared to standard ARQ.
*   **Usage:** Widely used in modern wireless communication systems like 3G (HSDPA/HSUPA), 4G (LTE), and 5G.

## Protocol Verification

Protocol verification is the process of formally checking whether a protocol design is correct, meaning it meets its specifications and is free from design flaws like deadlocks, livelocks, unspecified receptions, or other undesirable behaviors.

**Why Verify Protocols?**
*   Network protocols can be complex, involving interactions between multiple entities, concurrency, and asynchronous events.
*   Design errors can lead to network failures, security vulnerabilities, or poor performance.
*   Formal verification provides a higher degree of confidence in the protocol's correctness than informal testing alone.

**Common Modeling Techniques for Protocol Verification:**

1.  **Finite State Machine (FSM) Models:**
    *   **Concept:** A protocol entity (sender or receiver) is modeled as an FSM.
        *   **States:** Represent the different conditions or stages the entity can be in (e.g., "waiting for ACK," "ready to send").
        *   **Transitions:** Represent changes from one state to another, triggered by events.
        *   **Events:** Can be internal (e.g., timer expiry) or external (e.g., arrival of a frame, reception of a packet from Network Layer).
        *   **Actions:** Operations performed during a transition (e.g., send a frame, start a timer, pass data to Network Layer).
    *   **System Model:** The overall system is modeled as a set of interacting FSMs (e.g., sender FSM, receiver FSM, and possibly a channel FSM).
    *   **Verification Process:**
        *   **Reachability Analysis:** Explores all possible global states (combinations of states of individual FSMs) reachable from an initial state.
        *   This analysis can detect:
            *   **Deadlocks:** A global state where no further transitions are possible, and no useful work is being done (e.g., both sender and receiver are waiting for each other indefinitely).
            *   **Livelocks:** The system keeps making transitions, but no useful progress is made (e.g., continuously retransmitting and receiving the same frames without advancing).
            *   **Unspecified Receptions:** A state where an entity receives an event for which no transition is defined.
            *   **State Space Explosion:** The number of global states can become very large for complex protocols, making exhaustive analysis difficult.
    *   **Example:** Verifying Stop-and-Wait ARQ.
        *   Sender FSM states: "Ready to send frame 0", "Waiting for ACK 0", "Ready to send frame 1", "Waiting for ACK 1".
        *   Receiver FSM states: "Expecting frame 0", "Expecting frame 1".
        *   Events: Packet arrival from NL, frame arrival, ACK arrival, timeout.
        *   Transitions: Send frame, send ACK, start timer.
    *   **Tools:** SPIN, UPPAAL (for timed automata, an extension of FSMs).

2.  **Petri Net Models:**
    *   **Concept:** A bipartite directed graph with two types of nodes: **places** (circles) and **transitions** (bars or boxes). Arcs connect places to transitions or transitions to places. **Tokens** (dots) reside in places and represent the state of the system.
        *   **Places:** Represent conditions or resources.
        *   **Transitions:** Represent events or actions. A transition can "fire" if all its input places (connected by arcs pointing to the transition) have at least one token.
        *   **Firing a Transition:** Consumes one token from each input place and produces one token in each output place (connected by arcs pointing from the transition).
        *   **Marking:** The distribution of tokens in places at a given time defines the state (marking) of the Petri net.
    *   **Modeling Protocols:**
        *   Places can model states of protocol entities, message queues, or conditions (e.g., "frame sent," "ACK received").
        *   Transitions can model events like sending/receiving messages, timeouts, or processing steps.
    *   **Advantages over FSMs (for some aspects):**
        *   Better at explicitly modeling concurrency and synchronization.
        *   Can represent resource sharing and parallelism more naturally.
        *   More compact representation for some concurrent systems.
    *   **Verification Techniques:**
        *   **Reachability Graph (similar to FSMs):** Nodes are markings, arcs are transition firings. Can be analyzed for properties like boundedness (number of tokens in a place never exceeds a limit), liveness (a certain transition can always eventually fire), deadlocks.
        *   **Invariant Analysis:** Finding properties that hold true for all reachable markings (e.g., a place invariant: a weighted sum of tokens in a set of places is always constant).
        *   **Analysis of properties like:** Deadlock-freeness, boundedness, liveness, mutual exclusion.
    *   **Types of Petri Nets:**
        *   **Basic Petri Nets (PNs)**
        *   **Colored Petri Nets (CPNs):** Tokens can have data values (colors), making models more powerful and concise for complex systems. CPN Tools is a popular tool.
        *   **Timed Petri Nets:** Incorporate time delays associated with transitions or places.
    *   **Example:** Modeling a producer-consumer scenario or a communication channel with limited buffer capacity.

**Other Verification Approaches:**
*   **Temporal Logic Model Checking:** Expressing desired properties (e.g., "a request will eventually be granted") using temporal logic (like LTL or CTL) and automatically checking if the protocol model satisfies these properties.
*   **Theorem Proving:** Using formal logic and proof assistants (e.g., Coq, Isabelle/HOL) to prove protocol properties. Requires more manual effort but can handle infinite state systems.

**Challenges in Protocol Verification:**
*   **State Space Explosion:** The primary challenge for FSM and Petri net reachability analysis.
*   **Modeling Complexity:** Accurately capturing all relevant aspects of a protocol in a formal model.
*   **Abstraction:** Deciding what details to include or abstract away in the model.
*   **Specification:** Clearly defining what the protocol is supposed to achieve (its properties).

## ARP/RARP

Address Resolution Protocol (ARP) and Reverse Address Resolution Protocol (RARP) are utility protocols that operate between Layer 2 (Data Link) and Layer 3 (Network) – often considered part of the Network Layer or an interface between the two. They deal with mapping between L3 logical addresses (IP addresses) and L2 physical addresses (MAC addresses).

### Address Resolution Protocol (ARP)

*   **Problem:** When a host (e.g., Host A) wants to send an IP packet to another host (e.g., Host B) on the *same physical network (LAN)*, Host A knows Host B's IP address (the destination IP in the packet). However, to create the Layer 2 frame (e.g., Ethernet frame), Host A needs Host B's MAC address to put in the frame's destination MAC address field.
*   **Purpose of ARP:** To dynamically discover the MAC address of a host/router on the local network when its IP address is known.
*   **How ARP Works:**
    1.  **ARP Request (Broadcast):**
        *   Host A wants to find the MAC address for IP address `IP_B`.
        *   Host A constructs an **ARP Request** packet. This packet contains:
            *   Sender's MAC address (`MAC_A`)
            *   Sender's IP address (`IP_A`)
            *   Target MAC address (initially unknown, often set to `00:00:00:00:00:00`)
            *   Target IP address (`IP_B`)
        *   This ARP Request is encapsulated in an Ethernet frame.
            *   Source MAC: `MAC_A`
            *   Destination MAC: **Broadcast MAC address** (`FF:FF:FF:FF:FF:FF`)
            *   EtherType: `0x0806` (for ARP)
        *   The frame is broadcast on the LAN, so every device on the LAN receives and processes it.
    2.  **ARP Reply (Unicast):**
        *   All devices on the LAN receive the ARP Request.
        *   Each device checks if the "Target IP address" in the ARP Request matches its own IP address.
        *   Only Host B (whose IP is `IP_B`) will find a match.
        *   Host B then constructs an **ARP Reply** packet. This packet contains:
            *   Sender's MAC address (`MAC_B` - this is the information Host A was looking for)
            *   Sender's IP address (`IP_B`)
            *   Target MAC address (`MAC_A` - taken from the ARP Request)
            *   Target IP address (`IP_A` - taken from the ARP Request)
        *   This ARP Reply is encapsulated in an Ethernet frame.
            *   Source MAC: `MAC_B`
            *   Destination MAC: `MAC_A` (unicast, directly to Host A)
            *   EtherType: `0x0806`
        *   Host B sends this frame directly to Host A.
    3.  **ARP Cache Update:**
        *   Host A receives the ARP Reply and extracts `MAC_B`.
        *   Host A stores the mapping (`IP_B` -> `MAC_B`) in its **ARP cache** (a table in memory). This cache has a timeout for entries (e.g., a few minutes) to handle changes in MAC addresses or IP addresses.
        *   Now Host A can send its IP packet to Host B by creating a frame with destination MAC `MAC_B`.
        *   Other hosts that received the ARP Request might also cache `IP_A` -> `MAC_A` from the request (this is sometimes called "gratuitous ARP learning"). Host B also caches `IP_A` -> `MAC_A`.
*   **ARP Cache:**
    *   Each host and router maintains an ARP cache.
    *   Before sending an ARP Request, a host first checks its cache. If a mapping exists and is valid, ARP is not needed.
    *   Entries are timed out and removed to ensure freshness.
*   **Gratuitous ARP:**
    *   An ARP Request or Reply sent by a host for its own IP address without being prompted by a request.
    *   **Uses:**
        1.  **Duplicate IP Address Detection:** When a host boots up or its IP changes, it can send a gratuitous ARP for its own IP. If another host replies, it indicates an IP conflict.
        2.  **ARP Cache Update:** When a host changes its MAC address (e.g., NIC replacement) or IP address, it can send a gratuitous ARP to update the ARP caches of other devices on the LAN quickly.
        3.  Used in some high-availability setups (e.g., VRRP) for failover.

**ARP Packet Format (Simplified):**
| Field                  | Size (bytes) | Description                                      |
| ---------------------- | ------------ | ------------------------------------------------ |
| Hardware Type          | 2            | e.g., 1 for Ethernet                             |
| Protocol Type          | 2            | e.g., 0x0800 for IPv4                            |
| Hardware Address Length| 1            | e.g., 6 for MAC address                          |
| Protocol Address Length| 1            | e.g., 4 for IPv4 address                         |
| Opcode                 | 2            | 1 for ARP Request, 2 for ARP Reply              |
| Sender Hardware Address| HLEN         | MAC address of the sender of this ARP packet     |
| Sender Protocol Address| PLEN         | IP address of the sender of this ARP packet      |
| Target Hardware Address| HLEN         | MAC address of the target (filled in reply)      |
| Target Protocol Address| PLEN         | IP address of the target                         |

**ARP is for Local Network Communication:**
*   ARP operates only within a single broadcast domain (LAN segment).
*   Routers do **not** forward ARP broadcasts from one network to another.
*   If Host A wants to send a packet to Host C on a *different* network, Host A will ARP for the MAC address of its **default gateway router**, not Host C. The router then handles forwarding to Host C (possibly using ARP on Host C's network).

**Variations/Related Concepts:**
*   **Proxy ARP:** A router can respond to ARP Requests on behalf of hosts on another network that it is connected to. This can make hosts on different physical segments appear to be on the same segment. Can be useful but also complex to manage.
*   **Inverse ARP (InARP):** Used in Frame Relay networks to discover the IP address of a remote station when its Data Link Connection Identifier (DLCI - the L2 address in Frame Relay) is known.
*   **Reverse ARP (RARP):** Discussed next.

### Reverse Address Resolution Protocol (RARP)

*   **Problem:** A diskless workstation (or a device that doesn't have its IP address stored locally) boots up. It knows its own MAC address (from its NIC) but does not know its IP address.
*   **Purpose of RARP:** To allow a host to discover its IP address when it knows its MAC address. It's the reverse of ARP.
*   **How RARP Works (Similar to ARP but reversed roles of IP/MAC):**
    1.  **RARP Request (Broadcast):**
        *   The diskless client (Host X) constructs a **RARP Request** packet. This packet contains:
            *   Sender's MAC address (`MAC_X`)
            *   Sender's IP address (unknown, often `0.0.0.0`)
            *   Target MAC address (`MAC_X` - its own MAC address for which it wants the IP)
            *   Target IP address (unknown, `0.0.0.0`)
        *   This RARP Request is encapsulated in an Ethernet frame.
            *   Source MAC: `MAC_X`
            *   Destination MAC: **Broadcast MAC address** (`FF:FF:FF:FF:FF:FF`)
            *   EtherType: `0x8035` (for RARP)
        *   The frame is broadcast on the LAN.
    2.  **RARP Reply (Unicast or Broadcast):**
        *   A dedicated **RARP server** on the LAN listens for RARP Requests.
        *   The RARP server has a pre-configured table mapping MAC addresses to IP addresses.
        *   When the RARP server receives the request from `MAC_X`, it looks up `MAC_X` in its table to find the corresponding `IP_X`.
        *   The RARP server constructs a **RARP Reply** packet:
            *   Sender's MAC address (RARP Server's MAC)
            *   Sender's IP address (RARP Server's IP)
            *   Target MAC address (`MAC_X`)
            *   Target IP address (`IP_X` - the assigned IP address for the client)
        *   This RARP Reply is encapsulated in an Ethernet frame.
            *   Source MAC: RARP Server's MAC
            *   Destination MAC: `MAC_X` (unicast to the client)
        *   The RARP server sends this frame to the client.
    3.  **Client Configuration:**
        *   The client Host X receives the RARP Reply and extracts its assigned IP address (`IP_X`).
        *   It can now configure its network interface with this IP address.
*   **Limitations of RARP:**
    *   Requires a dedicated RARP server on each LAN segment because RARP requests are L2 broadcasts and not forwarded by routers.
    *   RARP only provides the IP address. It doesn't provide other crucial information like subnet mask, default gateway address, or DNS server addresses.
    *   The RARP packet format is minimal and cannot be easily extended to carry this extra information.
    *   The RARP server needs manual configuration of MAC-to-IP mappings.

**Superseded by Modern Protocols:**
RARP is largely obsolete and has been replaced by more comprehensive and flexible protocols for dynamic host configuration:
*   **BOOTP (Bootstrap Protocol):** An improvement over RARP. Uses UDP/IP. Can provide more information (IP, gateway, DNS server, boot file name). BOOTP requests can be relayed across routers by a BOOTP relay agent.
*   **DHCP (Dynamic Host Configuration Protocol):** The current standard. An extension of BOOTP. Provides fully dynamic IP address allocation (leasing), and a wealth of configuration parameters. DHCP is highly flexible and widely used.

While ARP is still fundamental for IPv4 networking on LANs, RARP is a legacy protocol primarily of historical interest when discussing the evolution of network configuration methods. 