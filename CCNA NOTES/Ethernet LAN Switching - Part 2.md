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


### Question

**After PC1 sends an ARP Request, what happens next? Does PC2 send an ARP Reply first, or does it immediately send the actual data to PC1? Is the data sent by PC2 called an ARP Reply?**

### Answer

**No.** PC2 does **not** send the actual data as an ARP Reply.

The sequence is as follows:

### Step 1: PC1 Sends an ARP Request (Broadcast)

PC1 broadcasts the following message to every device on the LAN:

```text
Who has 192.168.1.20?
Tell 192.168.1.10.
```

Since the destination MAC address is unknown, the ARP Request is sent as a **broadcast**, so every device on the local network receives it.

---

### Step 2: PC2 Sends an ARP Reply (Unicast)

PC2 checks the ARP Request and sees that the requested IP address (**192.168.1.20**) belongs to it.

PC2 then sends a **unicast** reply directly to PC1:

```text
I am 192.168.1.20.
My MAC address is BB-BB-BB-BB-BB-BB.
```

This message is called the **ARP Reply** (or **ARP Response**).

> **Important:** The ARP Reply **only contains PC2's MAC address**. It does **not** contain the actual application data.

---

### Step 3: PC1 Sends the Actual Data

After receiving the ARP Reply, PC1 stores PC2's MAC address in its **ARP cache**.

Now PC1 can send the actual data to PC2.

Example:

```text
Destination MAC: BB-BB-BB-BB-BB-BB
Source MAC:      AA-AA-AA-AA-AA-AA

Payload:
Hello!
```

---

### Communication Flow

```text
PC1                             PC2
 |                               |
 |-- ARP Request (Broadcast) --->|
 |  "Who has 192.168.1.20?"      |
 |                               |
 |<-- ARP Reply (Unicast) -------|
 |  "My MAC is BB-BB-BB-BB..."   |
 |                               |
 |------ Actual Data ----------->|
 |  "Hello"                      |
```

---

### Key Points for CCNA

- **ARP Request** → **Broadcast** → "Who has this IP address?"
- **ARP Reply** → **Unicast** → "This is my MAC address."
- **Actual data** is sent **only after** the ARP Reply is received.
- The **ARP Reply is not the actual data**; it is only used to provide the destination MAC address.
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
