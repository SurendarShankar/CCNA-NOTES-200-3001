# 21. SPANNING TREE PROTOCOL (STP) : PART 2

STP STATES

![image](https://github.com/psaumur/CCNA/assets/106411237/5c9a17ff-b0d6-455c-8677-5144dd5a0048)


- ROOT / DESIGNATED PORTS remain STABLE in a FORWARDING state
- NON-DESIGNATED PORTS remain STABLE in a BLOCKING state
- LISTENING and LEARNING are TRANSITIONAL states which are passed through when an interface is activated, or when a BLOCKING PORT must transition to a FORWARDING state due to a change in network topology.

**STP BPDU — Simple Explanation**

**BPDU = Bridge Protocol Data Unit**

It is a **special message sent between switches** to exchange information for **STP (Spanning Tree Protocol)**.

Think of it as:

> **Switch → "Hello, this is who I am, and this is the path I know." → Other Switch**

**What information does a BPDU contain?**

- **Root Bridge ID** → Who is the current Root Bridge?
- **Root Path Cost** → How far is the switch from the Root Bridge?
- **Sender/Bridge ID** → Which switch sent this BPDU?
- **Port ID** → Which port sent it?
- **Timers** → STP timing information

**1) BLOCKING / STABLE**

- NON-DESIGNATED PORTS are in a BLOCKING state
- Interfaces in a BLOCKING state are effectively disabled to prevent loops
- Interfaces in a BLOCKING state do NOT Send/Receive regular network traffic
- Interfaces in a BLOCKING state do NOT forward STP BPDUs
- Interfaces in a BLOCKING state do NOT learn MAC ADDRESSES

**2) LISTENING / TRANSITIONAL**

- After the BLOCKING state, interfaces with the DESIGNATED or ROOT role enter the LISTENING state
- ONLY DESIGNATED or ROOT PORTS enter the LISTENING state (NON-DESIGNATED PORTS are ALWAYS BLOCKING)
- The LISTENING state is 15 seconds long by Default. This is determined by the FORWARD DELAY TIMER
- Interfaces in a LISTENING state do NOT Send / Receive regular network traffic
- Interfaces in a LISTENING state ONLY Forward/Receive STP BPDUs
- Interfaces in a LISTENING state does NOT learn MAC ADDRESSES from regular traffic that arrives on the interface

**3) LEARNING / TRANSITIONAL**

- After the LISTENING state, a DESIGNATED or ROOT port will enter the LEARNING state
- The LEARNING state is 15 seconds long by Default. This is determined by the FORWARD DELAY TIMER (same one used for both LISTENING and LEARNING states)
- Interfaces in a LEARNING state do NOT Send / Receive regular network traffic
- Interfaces in a LEARNING state ONLY Sends/Receives STP BPDUs
- Interfaces in a LEARNING state **learns** MAC ADDRESSES from regular traffic that arrives on the interface

**4) FORWARDING / STABLE**

- ROOT and DESIGNATED PORTS are in a FORWARDING state
- A PORT in the FORWARDING state operate as NORMAL
- A PORT in the FORWARDING state Sends/Receives regular network traffic
- A PORT in the FORWARDING state Sends/Receives STP BPDUs
- A PORT in the FORWARDING state **learns** MAC ADDRESSES

SUMMARY : 

![image](https://github.com/psaumur/CCNA/assets/106411237/f4cea5ca-b90a-423e-9160-f206b8b1621d)


---

STP TIMERS

![image](https://github.com/psaumur/CCNA/assets/106411237/a174469f-9e75-4645-aff8-d4bfe46fb207)


💡 SWITCHES do NOT forward the BPDUs out of their ROOT PORTS and NON-DESIGNATED PORTS - ONLY their DESIGNATED PORTS !!!


MAX AGE TIMER:

- If another BPDU is received BEFORE MAX AGE TIMER counts down to 0, the TIME will RESET to 20 Seconds and no changes will occur.
- If another BPDU is not received, the MAX AGE TIMER counts down to 0 and the SWITCH will re-evaluate it’s STP choices, including ROOT BRIDGE, LOCAL ROOT, DESIGNATED, and NON-DESIGNATED PORTS.
- If a NON-DESIGNATED PORT is selected to become a DESIGNATED or ROOT PORT, it will transition from the BLOCKING state to the LISTENING state (15 Seconds), LEARNING state (15 Seconds), and then finally the FORWARDING state.
    - So… it can take 50 Seconds for a BLOCKING interface to transition to FORWARDING! (MAX AGE TIMER  + (LISTENING + LEARNING 15 Second timers))
- These TIMERS and TRANSITIONAL STATES are to make sure that LOOPS are not accidentally created by an INTERFACE moving to FORWARDING STATE too soon

 HOWEVER …

💡 A FORWARDING interface can move DIRECTLY to a BLOCKING state (there is no worry about creating a loop)

💡 A BLOCKING interface can NOT move DIRECTLY to a FORWARDING state. It MUST go through the LISTENING and LEARNING states first!


---

STP BPDU (BRIDGE PROTOCOL DATA UNIT)

Ethernet Header of a BPDU

![image](https://github.com/psaumur/CCNA/assets/106411237/0e68839f-c4ec-448b-8876-791212462009)


💡 PVST+ uses the MAC ADDRESS : 

01 : 00 : 0c : cc : cc : cd

PVST = ONLY ISL Trunk Encapsulation

PVST+ = Supports 802.1Q

💡 Regular STP (not Cisco’s PVST+) uses the MAC ADDRESS : 

01 : 80 : c2 : 00 : 00 : 00

💡 The STP TIMERS on the ROOT BRIDGE determine ALL STP TIMERS for the entire network!

---

STP OPTIONAL FEATURES (STP TOOLKIT)

PORTFAST:

- Can be Enabled on INTERFACES which are connected to END HOSTS

💡 PORTFAST allows a PORT to move immediately to the FORWARDING state, bypassing LISTENING and LEARNING

- If used, it MUST be ENABLED only on PORTS connected to END HOSTS
- If ENABLED on a PORT connected to another SWITCH, it could cause a LAYER 2 LOOP

![image](https://github.com/psaumur/CCNA/assets/106411237/43c91f09-0d9f-4b81-b5a2-f02003e25b88)


You can also ENABLE PORTFAST with the following command:

💡 SW1(config)# spanning-tree portfast default

This ENABLES PORTFAST on ALL ACCESS PORTS (not TRUNK PORTS)

BPDU GUARD:

- If an INTERFACE with BPDU GUARD ENABLED receives a BPDU from another SWITCH, the INTERFACE will be SHUT DOWN to prevent loops from forming.

![image](https://github.com/psaumur/CCNA/assets/106411237/00c61767-72b4-4d51-b964-f76b6f4f6ae9)


You can also ENABLE BPDU GUARD with the following command:

💡 SW1(config)# spanning-tree portfast bpduguard default


This ENABLES BPDU GUARD on all PORTFAST-enabled INTERFACES

ROOT GUARD / LOOP GUARD:

![image](https://github.com/psaumur/CCNA/assets/106411237/bb38aedc-df38-4d76-b6cb-30319e74ecc1)


You probably do NOT have to know these STP optional features (or others such as UplinkFast, Backbone Fast, etcetera) for the CCNA. 

BUT…

💡 Make sure you know PORTFAST and BPDU GUARD.

---

STP CONFIGURATION

Command to CONFIGURE Spanning-Tree mode on a SWITCH

![image](https://github.com/psaumur/CCNA/assets/106411237/f29e2f41-3fac-463c-ab14-bb2d2f49816d)


Modern Cisco SWITCHES run **rapid-pvst**, by default

---

CONFIGURE THE PRIMARY ROOT BRIDGE

Command to CONFIGURE Spanning-Tree PRIMARY ROOT BRIDGE on a SWITCH

![image](https://github.com/psaumur/CCNA/assets/106411237/e90f16ad-c85c-4868-bbf4-9095c0abd581)


Confirm with “(do) show spanning-tree”

Can see in the above example, SW3 has become the “root”

- The “spanning-tree vlan <vlan-number> root primary” command sets the STP PRIORITY to 24576. If another SWITCH already has a priority number lower than 24576, it sets this SWITCH’s priority to 4096 LESS THAN the other SWITCH’s Priority (remember STP PART 1 lecture)

**`spanning-tree vlan root primary`**

The `spanning-tree vlan 10 root primary` command is used to make a switch the **Root Bridge** for VLAN 10.

**Important:**

**Lower STP Priority = Better chance of becoming Root Bridge**

**Case 1: No switch has a lower priority**

Cisco sets the priority to **24576**.

Example:

- SW1 = 32768
- SW2 = 32768

You run the command on SW1:

`SW1 → 24576`

Now SW1 has the lowest priority.

**SW1 becomes the Root Bridge.**

**Case 2: Another switch already has a lower priority**

Example:

- SW1 = 32768
- SW2 = **20480**

You run the command on SW1.

Cisco sees:

> SW2 already has a priority lower than 24576.

So Cisco sets SW1's priority to **4096 less than SW2**.

**20480 − 4096 = 16384**

Now:

- SW1 = **16384**
- SW2 = 20480

SW1 has the lowest priority.

**SW1 becomes the Root Bridge.**

**Easy Memory:**

- **Root Primary → Try to become Root**
- Normal target = **24576**
- If another switch is already lower → **4096 lower than that switch**
- **Lowest priority wins**

---

SECONDARY ROOT BRIGE (backup ROOT BRIDGE)

Command to CONFIGURE Spanning-Tree SECONDARY ROOT BRIDGE on a SWITCH

![image](https://github.com/psaumur/CCNA/assets/106411237/7d28f782-4673-4bc8-9aae-999aeac90685)



- The “spanning-tree vlan <vlan-number> root secondary” command sets the STP PRIORITY to 28672 (exactly 4096 higher than 24576).

---

VLAN 1 TOPOLOGY running PVST+

![image](https://github.com/psaumur/CCNA/assets/106411237/880a4cc7-e472-4764-a68b-a62288066796)


SW1 WAS the PRIMARY ROOT BRIDGE but : 

- We have configured SW3 to be the PRIMARY
- We have configured SW2 to be the SECONDARY

The TOPOLOGY for VLAN 2, however, won’t be the same. It will be the OLD Topology.

![image](https://github.com/psaumur/CCNA/assets/106411237/2cedeb36-27f1-4984-96e7-28ab70957c51)


WHY?
Because we made changes ONLY to the TOPOLOGY found in VLAN 1 (see the commands we used)

---

CONFIGURE STP PORT SETTINGS

![image](https://github.com/psaumur/CCNA/assets/106411237/58af0a8d-eeb4-4c34-8b54-6b8ff511695c)


“cost” = “ROOT COST”

“port-priority” = “PORT PRIORITY”

---

# PortFast

* When a host connects to a switch port, by default it takes **30 seconds** before the port can send/receive data.

* **PortFast** allows a switch port to immediately enter the **STP Forwarding state**, bypassing the **Listening** and **Learning** states.

* It can be configured in two ways:

### 1. Interface Configuration Mode

```text
SW1(config-if)# spanning-tree portfast [edge]
```

* Enables PortFast only on the **individual interface**.
* It is active only when the interface is in **access mode**.

### 2. Global Configuration Mode

```text
SW1(config)# spanning-tree portfast [edge] default
```

* Enables PortFast on **all access ports**.
* You can disable PortFast on a specific port using:

```text
SW1(config-if)# spanning-tree portfast disable
```

### Important

* **Do NOT configure PortFast on ports connected to another switch**, as it can cause temporary **Layer 2 loops**.
* PortFast can be enabled on a trunk port using:

```text
SW1(config-if)# spanning-tree portfast [edge] trunk
```

---

## BPDU Guard

- PortFast should only be enabled on ports connected to non-switch devices (end hosts, routers) that don’t send BPDUs.
- A PortFast-enabled port still sends BPDUs and will operate like a regular STP port if it receives BPDUs from a neighbor.
- If an end user carelessly connects a switch to a port meant for end hosts, it could affect the STP topology.
- BPDU Guard protects the network from unauthorized switches being connected to ports intended for end hosts.
- If the port receives a BPDU, it enters the error-disabled (err-disabled) state, effectively disabling the port.
- Per-port:
  `SW1(config-if)# spanning-tree bpduguard enable`
- Default:
  `SW1(config)# spanning-tree portfast [edge] bpduguard default`
- Enables BPDU Guard on all PortFast-enabled ports.
- Use `spanning-tree bpduguard disable` to disable it on specific ports.
- An err-disabled port can be re-enabled in two ways:
  1. Manual: `shutdown` and `no shutdown`
  2. Automatic: ErrDisable Recovery
     - `SW1(config)# errdisable recovery cause bpduguard`
- In either case, make sure you fix the underlying problem that caused the port to be err-disabled.

## BPDU Filter

- BPDU Filter prevents a port from sending BPDUs.
- Unlike BPDU Guard, it does not disable the port if it receives a BPDU.
- Per-port:
  `SW1(config-if)# spanning-tree bpdufilter enable`
- The port will ignore any BPDUs it receives. Use with caution!
- Default:
  `SW1(config)# spanning-tree portfast [edge] bpdufilter default`
- Enables BPDU Filter on all PortFast-enabled ports.
- If the port receives a BPDU, PortFast and BPDU Filter are disabled, and it operates as a normal STP port.
- Use `spanning-tree bpdufilter disable` to disable it on specific ports.
  
#### This was a example explination for this line : If the port receives a BPDU, PortFast and BPDU Filter are disabled, and it operates as a normal STP port
BPDU Filter:
  
Think of BPDU Filter as:

"Don't send/receive STP BPDU messages on this PortFast port."

Why do we need it?

Normally, a PortFast port is connected to an end device like:

- PC
- Printer
- Server

So the switch assumes:

"This port is connected to an end device, not another switch."

BPDU Filter prevents BPDUs from being exchanged on PortFast ports.

Command:

spanning-tree portfast bpdufilter default

This enables BPDU Filter on PortFast-enabled ports.

Example:

SW1 ─────── PC
       PortFast
       BPDU Filter

The PortFast port normally doesn't exchange BPDUs.

What if someone connects a switch?

SW1 ─────── SW2
       PortFast port
       + BPDU Filter

If SW2 sends a BPDU, the port detects the BPDU and returns to normal STP operation.

Simple memory trick:

PortFast     = Skip STP waiting
BPDU Filter  = Filter/stop BPDUs
BPDU received = "This may be a switch → return to normal STP."

Specific command:

spanning-tree bpdufilter disable

This disables BPDU Filter on a specific interface.

Important:

BPDU Filter is not the main security feature for an unauthorized switch.

BPDU Guard is designed for that purpose.

BPDU Guard → If a BPDU is received on a PortFast port, the port can be placed into an err-disabled state.

---

# Root Guard

- When selecting a LAN's Root Bridge, you should consider the following:
  - Optimal traffic flow
  - Minimize latency
  - Minimize congestion
  - Stability and reliability

- Within your own LAN, you can easily control the Root Bridge by setting its priority to 0.

- There are cases where you might connect your switches to other switches outside of your control (e.g., service provider + client).

- Root Guard can be configured on specific ports to prevent them from accepting superior BPDUs from switches outside of your control.

- Use the following command to enable Root Guard on a port:
  `SW(config-if)# spanning-tree guard root`

- There is no command to enable it by default from global config mode.

- Root Guard prevents a port from becoming a Root Port if it receives a superior BPDU.

- If the port receives a superior BPDU, it becomes Broken (BKN) / Root Inconsistent (ROOT_Inc).

- If the port stops receiving superior BPDUs, it will automatically recover.
  
- To re-enable a port disabled by Root Guard, you must solve the issue that disabled the port.
- The disabled port must stop receiving superior BDPUs.
- Tell the customer to increase the priority value of their switch.
  4097:5254.0018.2bbd
• Once the superior BPDUs received by SW2 G0/2 and SW3 G0/3 age out, the ports will automatically be re-enabled.
• A BPDU’s Max Age is 20 seconds by default.

### Question

“When a switch stops receiving superior BPDUs, what happens when the 20-second Max Age timer expires? Does the switch recalculate the Root Bridge and automatically re-enable a Root Guard-protected port, or is there another process involved?”

**1. Max Age reaches 20 seconds**

- Suppose SW2 receives a superior BPDU:
```flow chat
Superior BPDU
     ↓
SW2 G0/2
     ↓
Root Guard
     ↓
Root-Inconsistent
```

- Now the superior BPDUs stop.

- The last BPDU information is allowed to age out.

```java
Last superior BPDU
       ↓
   wait ~20 sec
       ↓
Max Age expires
       ↓
Old BPDU information is removed
```
2. Does SW2 then find a new Root Bridge?

- Yes, STP can recalculate the topology.

- SW2 looks at the BPDUs it is currently receiving from its other STP neighbors and determines:

* "Which switch should be the Root Bridge, and which port should be my Root Port?"

- So STP recalculates.

3. What happens to the Root-Guarded port?

- This is the important part:

- Once superior BPDUs are no longer being received, the Root Guard violation condition is cleared.

