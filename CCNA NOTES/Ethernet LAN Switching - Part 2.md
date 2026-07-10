# 6. ETHERNET LAN SWITCHING : PART 2

An ETHERNET FRAME looks like:

Ethernet Header --- DATA (Packet) --- Ethernet Trailer

![image](https://github.com/psaumur/CCNA/assets/106411237/27c1877f-57d7-44ea-8c64-b0ec2b308ad0)


The Ethernet Header contains 5 Fields:

Preamble -- SFD -- Destination -- Source -- Type/Length
7 bytes  -- 1 byte -- 6 bytes -- 6 bytes --   2 bytes

Ethernet Trailer contains 1 Field:

FCS (Frame Check Sequence) = 4 bytes

- The PREAMBLE + SFD is not usually considered part of the ETHERNET HEADER.

THEREFORE the size of the ETHERNET HEADER + TRAILER is 18 bytes

(6 + 6 + 2 + 4 bytes for the FRAME CHECK SEQUENCE)

---

The MINIMUM size for an ETHERNET FRAME (Header + Payload [PACKET] + Trailer) is 64 BYTES.

64 BYTES - 18 BYTES (Header + Trailer size) = 46 BYTES

THEREFORE the MINIMUM DATA PAYLOAD (PACKET) size is 46 BYTES!

IF the PAYLOAD is LESS than 46 BYTES then PADDING BYTES are added (padding bytes are a series of 0's) until it equals to 46 BYTES.

---

When a PC wants to send a packet to a destination, with a known IP address but an unknown MAC address, it first needs to send an ARP Request to learn that destination's MAC address.

![image](https://github.com/psaumur/CCNA/assets/106411237/e2d0e5d2-7c98-4671-b356-903132fd7525)


- ARP stands for 'Address Resolution Protocol'.
- It is used to discover the Layer 2 address (MAC address) of a known Layer 3 address (IP address)
- Consists of two messages:
    - ARP REQUEST (Source message)
    - ARP REPLY (Destination message)
- ARP REQUEST is BROADCAST = sent to all hosts on network, except the one it received the request from.

An ARP REQUEST frame has:

- Source IP Address
- Destination IP Address
- Source MAC address
- BROADCAST MAC Address - FFFF.FFFF.FFFF

An ARP REPLY frame has:

- Source IP Address
- Destination IP Address
- Source MAC address
- Destination MAC Address

ARP REPLY is a known UNICAST frame = Sent only to the host that sent the ARP REQUEST.

![image](https://github.com/psaumur/CCNA/assets/106411237/914cdf2a-c631-47e5-80f9-46e32ebed311)


---

PING

- A network utility that is used to test reachability
- Measures round-trip time
- Uses two messages:
    - ICMP Echo REQUEST
    - ICMP Echo REPLY
- Is UNICAST
- Command to use ping:
    - ping <ip-address>

By Default, a CISCO IOS sends 5 ICMP requests/replies
(Default size is 100-bytes)

- A period (.) is a failed ping
- An exclamation mark (!) is a successful ping

---

USEFUL CISCO IOS COMMANDS (from Privileged EXEC mode)

PC1# show arp // shows hosts ARP table

![image](https://github.com/psaumur/CCNA/assets/106411237/da199d21-4f41-485e-8917-ca8e3d789617)


---

SW1#show mac address-table // show the switches MAC table

![image](https://github.com/psaumur/CCNA/assets/106411237/c1cd95dd-7742-4703-9487-946652c95485)


Will show:

Vlan --- MAC Address --- Type --- Ports(interfaces)

(Vlan = Virtual Local Area Network)

---

![image](https://github.com/psaumur/CCNA/assets/106411237/657b054b-a90c-4e5f-8544-2a51082cb631)


SW1# clear mac address-table dynamic <optional MAC address>

// clears the entire switches MAC table.
// IF the optional MAC address is used, it will clear the SPECFIC MAC address.

SW1 #clear mac address-table dynamic interface <optional Interface>

// clears the MAC table entry of the Switch by it's **INTERFACE n**ame.

---
### For My Understandibg the diffrence

### ARP vs Ping

| Feature | **ARP (Address Resolution Protocol)** | **Ping (ICMP Echo Request/Reply)** |
|---------|----------------------------------------|------------------------------------|
| **Purpose** | Finds the **MAC address** of a device using its IP address. | Checks whether another device is **reachable** on the network. |
| **Works With** | IP Address → MAC Address | Device ↔ Device communication |
| **Protocol** | ARP | ICMP (Internet Control Message Protocol) |
| **OSI Layer** | Layer 2 (Data Link) *(works between Layer 2 and Layer 3)* | Layer 3 (Network Layer) |
| **Broadcast or Unicast?** | **ARP Request** = Broadcast<br>**ARP Reply** = Unicast | **Echo Request** = Unicast<br>**Echo Reply** = Unicast |
| **What does it return?** | The destination device's **MAC address**. | A reply showing the device is reachable and the round-trip time (latency). |

---

### What ARP Does

Suppose **PC1** wants to send data to **192.168.1.20**, but it only knows the IP address.

#### Step 1: ARP Request (Broadcast)

PC1 sends an ARP Request:

> **"Who has IP 192.168.1.20? Tell me your MAC address."**

#### Step 2: ARP Reply (Unicast)

PC2 replies:

> **"I have IP 192.168.1.20. My MAC address is AA:BB:CC:DD:EE:FF."**

#### Step 3: Store the MAC Address

PC1 stores the MAC address in its **ARP table** and then sends the actual data.

> **ARP is only used to discover the destination MAC address.**

---

### What Ping Does

After PC1 knows the destination MAC address (using ARP if necessary), it can send an **ICMP Echo Request**.

#### Step 1: Echo Request

PC1 sends a **Ping (ICMP Echo Request)** to PC2.

#### Step 2: Echo Reply

If PC2 is reachable, it sends back an **ICMP Echo Reply**.

#### Result

If the reply is received, the ping is successful. It confirms that the destination is reachable and reports the **round-trip time (latency)**.

> **Ping is used to test network connectivity, not to find MAC addresses.**

---

### Simple Example

- **ARP asks:** *"What is your MAC address?"*
- **Ping asks:** *"Are you alive and reachable?"*

---

### Easy Way to Remember

| **ARP** | **Ping** |
|---------|----------|
| Finds the **MAC address** of a known IP address. | Checks if a device is **reachable**. |
| Uses **ARP Request** and **ARP Reply**. | Uses **ICMP Echo Request** and **Echo Reply**. |
| Used before sending data on a local network if the MAC address is unknown. | Used to verify connectivity and measure response time. |
