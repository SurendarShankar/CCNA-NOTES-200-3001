# 25. RIP and EIGRP (IGP : DYNAMIC VECTOR)

ROUTING INFORMATION PROTOCOL (RIP)

- Routing Information Protocol (Industry Standard)
- is a DISTANCE VECTOR IGP
    - uses Routing-By-Rumor logic to learn/share routes
- Uses HOP COUNT as it’s METRIC (One Router = One Hop)  Bandwidth is irrelevant
- MAX HOP COUNT is 15 (anything more is considered unreachable)
- Has THREE VERSIONS:
    - RIPv1 and RIPv2; used for IPv4
    - RIPng (RIP Next Generation) used for IPv6
- Uses TWO MESSAGE TYPES:
    - REQUEST :
        - To ask RIP-ENABLED neighbour ROUTERS to send their ROUTING TABLE
    - RESPONSE:
        - To SEND the LOCAL router’s ROUTING TABLE to neighbouring ROUTERS

By DEFAULT, RIP-Enabled ROUTERS will share their ROUTING TABLE every 30 seconds

RIPv1 and RIPv2

RIPv1:

- Only advertises *classful addresses* (Class A, Class B, Class C)
- Doesn’t support VLSM, CIDR
- Doesn’t include SUBNET MASK information in ADVERTISEMENTS (RESPONSE messages)
    - Example:
        - 10.1.1.0/24 will become 10.0.0.0 (Class A Address, so assumed to be /8)
        - 172.16.192.0/18 will become 172.16.0.0 (Class B Address, so assumed to be /16)
        - 192.168.1.40/30 will become 172.168.1.0 (Class C Address, so assumed to be /24)
- Messages are BROADCAST to 255.255.255.255

RIPv2:

- Supports VLSM, CIDR
- Includes SUBNET MASK information in ADVERTISEMENTS
- Messages are **multicast** to 224.0.0.9
    - Broadcast Messages are delivered to ALL devices on the local network
    - Multicast Messages are delivered only to devices to have joined that specific ***multicast group***

---

CONFIGURING RIP

![image](https://github.com/psaumur/CCNA/assets/106411237/1d14ec8b-121c-4666-b608-1e5d1889424c)

The **“network”** command tells the router to:

- Look for INTERFACES with an IP ADDRESS that is in the specific RANGE
- ACTIVATES RIP on the INTERFACES that fall in the RANGE
- Form ADJACENCIES with connected RIP neighbors
- Advertise the **NETWORK PREFIX of the INTERFACE** (NOT the prefix in the “network” command)

The OSPF and EIGRP **“network”** commands operate in the same way

Because the RIP “network” command is CLASSFUL. It will automatically convert to CLASSFUL networks

- 10.0.0.0 is assumed to be 10.0.0.0/8
- R1 will look for ANY INTERFACES with an IP ADDRESS that matches 10.0.0.0/8 (because it is /8 it only needs to match the FIRST 8 bits)
- 10.0.12.1 and 10.0.13.1 both match SO RIP is ACTIVATED on G0/0 and G0/1
- R1 then forms ADJACENCIES with its neighbors R2 and R3
- R1 ADVERTISES 10.0.12.0/30 and 10.0.13.0/30 (NOT 10.0.0.0/8) to it’s RIP neighbors

![image](https://github.com/psaumur/CCNA/assets/106411237/2a9452f0-b48f-499d-938f-0a3db5ff6587)

- Because the “network” command is CLASSFUL, 172.16.0.0 is assumed to be 172.16.0.0/16
- R1 will look for ANY INTERFACES that match 172.16.0.0/16
- 172.16.1.14 matches, so R1 will ACTIVATE RIP on G2/0
- There are NO RIP neighbors connected to G2/0 so no NEW ADJACENCIES are formed
- Although there are NO RIP neighbors, R1 will still send ADVERTISEMENTS out of G2/0.
- This is unnecessary traffic, so G2/0 should be configured as a **passive interface**

**A Passive Interface**

> A **passive interface** in a routing protocol means:
>
> **The router stops sending routing protocol messages out of that interface, but it can still advertise the connected network through other interfaces.**


![image](https://github.com/psaumur/CCNA/assets/106411237/634f4c6b-291c-4a21-8ae2-c8283044efce)

- the “passive-interface” command tells the ROUTER to stop sending RIP advertisements out of the specified interface (G2/0)
- However, the ROUTER will continue to ADVERTISE the network prefix of the interface (172.16.1.0/28) to it’s RIP neighbors (R2, R3)
- You should ALWAYS use this command on INTERFACES which don’t have any RIP neighbors
- EIGRP and OSPF both have the same passive INTERFACE functionality, using the same command.

---

HOW TO ADVERTISE A DEFAULT ROUTE INTO RIP

![image](https://github.com/psaumur/CCNA/assets/106411237/57de003e-0e8e-48c7-bb72-fbe25208d847)

![image](https://github.com/psaumur/CCNA/assets/106411237/1c500efd-e96b-4e49-b1f4-f99c54b0e877)

To SHARE this DEFAULT ROUTE with R1’s RIP neighbors, using this command:

![image](https://github.com/psaumur/CCNA/assets/106411237/799d818a-06cc-4f29-8c74-c67639c9d014)

RIP doesn’t care about interface AD cost (RIP cost is 120), only “hops”.

Since both have an equal number of “hops”, both paths appear in the DEFAULT ROUTE (Gateway of Last Resort)

![image](https://github.com/psaumur/CCNA/assets/106411237/1deccb54-02e0-4d3b-b203-277d656504b3)

---

“show ip protocols” (for RIP)

![image](https://github.com/psaumur/CCNA/assets/106411237/b7ab4046-b6eb-4e19-b7eb-2c5d2889293a)

“Maximum path: 4” is the DEFAULT but can be changed with this command:

![image](https://github.com/psaumur/CCNA/assets/106411237/35d524bd-055d-4c5e-a84b-f507a87738e0)

“Distance” (AD) can be changed with this command (DEFAULT is 120)

![image](https://github.com/psaumur/CCNA/assets/106411237/5247942b-1d6b-419f-a4c7-75bfcca43fe6)

---

ENHANCED INTERIOR GATEWAY ROUTING PROTOCOL (EIGRP)

- Enhanced Interior Gateway Routing Protocol
- is a DISTANCE VECTOR IGP
- Was Cisco proprietary, but Cisco has now published it openly so other vendor can implement it on their equipment
- Considered an “advanced” / “hybrid” DISTANCE VECTOR ROUTING PROTOCOL
- Much faster than RIP in reacting to changes in the NETWORK
- Does NOT have the 15 ‘hop count’ limit of RIP
- Sends messages using MULTICAST ADDRESS **224.0.0.10 (Memorize this number)**
- Is the ONLY IGP that can perform **unequal**-cost load-balancing (by DEFAULT, it performs ECMP load-balancing over 4 paths like RIP)

---

CONFIGURATION OF EIGRP

![image](https://github.com/psaumur/CCNA/assets/106411237/f2b42631-bcb9-4f62-afe9-b7bb1e7e0d7e)

“router eigrp <Autonomous System number>”

- The AS (Autonomous System) number MUST MATCH between ROUTERS or they will NOT form an ADJACENCY and share ROUTE information
- Auto-summary might be ENABLED or DISABLED by DEFAULT; depending on the ROUTER/IOS version. If ENABLED, DISABLE it.
- The **“network”** command will assume a CLASSFUL ADDRESS, if you don’t specify the SUBNET MASK
- EIGRP uses a *wildcard mask* instead of a regular subnet mask

A WILDCARD MASK is an “inverted” SUBNET MASK

- All 1’s in the SUBNET MASK are 0 in the equivalent WILDCARD MASK.
- All 0s in the SUBNET MASK are 1 in the equivalent WILDCARD MASK.

![image](https://github.com/psaumur/CCNA/assets/106411237/f64e06d3-75ad-4f4f-b7d6-26f27ffae541)

“0” in the WILDCARD MASK = BITS MUST MATCH !

“1” in the WILDCARD MASK = Do not have to match

![image](https://github.com/psaumur/CCNA/assets/106411237/13130e3c-de62-4f80-9c7d-256a2ed47e74)

![image](https://github.com/psaumur/CCNA/assets/106411237/1aa2cd2c-397f-4f3b-86ed-81eddf2677a6)

![image](https://github.com/psaumur/CCNA/assets/106411237/500ac3b0-5d83-4691-ab94-06fd330a9111)

---

“show ip protocols” (for EIGRP)

![image](https://github.com/psaumur/CCNA/assets/106411237/f3f169da-d733-4da9-8d8a-c90e2077d8a7)

“Router ID”

ROUTER ID order of priority:

- Manual configuration
- Highest IP ADDRESS on a LOOPBACK INTERFACE
- Highest IP ADDRESS on a PHYSICAL INTERFACE

![image](https://github.com/psaumur/CCNA/assets/106411237/29757624-9e79-4878-8724-36d5da43f39b)

“Distance” (AD)

EIGRP has TWO VALUES:

- Internal = 90
- External = 170

MEMORIZE THESE VALUES!

“show ip route” (for EIGRP)

![image](https://github.com/psaumur/CCNA/assets/106411237/8216ceb6-0d3f-42e7-8e5b-46e810097fb8)

NOTE the large METRIC numbers. This is a DOWNSIDE to EIGRP - even on small networks!

---

EIGRP METRIC

- By DEFAULT, EIGRP uses BANDWIDTH and DELAY to calculate METRIC
- Default “K” values are:
    - K1 = 1, K2 = 0, K3 = 1, K4 = 0, K5 = 0

💡 Simplified calculation : METRIC = BANDWIDTH (Slowest Link) + DELAY (of ALL LINKS)

---

EIGRP TERMINOLOGY

- **Feasible Distance** = This ROUTER’s METRIC value to the ROUTE’s DESTINATION
- **Reported Distance** (aka **Advertised Distance**) = The neighbor’s METRIC value to the ROUTE’s DESTINATION

![image](https://github.com/psaumur/CCNA/assets/106411237/436ba2c2-43e7-4fea-a527-f88a8e4460bc)

- **Successor =** the ROUTE with the LOWEST METRIC to the DESTINATION (the best route)
- **Feasible Successor** = An alternate ROUTE to the DESTINATION (not the best route) which meets the *feasibility condition*

**FEASIBILITY CONDITION** : A ROUTE is considered a ***Feasible Successor*** if it’s ***Reported Distance*** is LOWER than the Successor ROUTE’s ***Feasible distance***

![image](https://github.com/psaumur/CCNA/assets/106411237/206db633-3a7e-4d11-bb80-029ea8107503)

---

EIGRP : UNEQUAL-COST LOAD-BALANCED

![image](https://github.com/psaumur/CCNA/assets/106411237/23a2045b-a925-4f75-b0f8-78cbae2aa1e2)

“maximum metric variance 1” = the DEFAULT value

Variance 1 = only ECMP (Equal-Cost Multiple Path) load-balancing will be performed

![image](https://github.com/psaumur/CCNA/assets/106411237/824dac1d-38dc-4e7e-bb48-b382918230ff)

Variance 2 = ***feasible successor*** routes with an FD up to 2x the ***successor*** route’s FD can be used to load-balance

---

#### *Example :*
**EIGRP Unequal-Cost Load Balancing**

Let's understand it with a simple EIGRP topology instead of just memorizing the rule.

Imagine this network:

                 R2
              /      \
          10 /        \ 20
            /          \
           R1            R3
            \           /
          15 \         / 5
                \     /
                  R4

R1 wants to reach the network behind R3.

There are two possible paths:

Path 1: R1 → R2 → R3
Path 2: R1 → R4 → R3

Suppose EIGRP calculates:

R1 → R2 → R3
FD = 20       ← Best path

R1 → R4 → R3
Total metric = 30

So R2 is the **Successor**.


**Step 1: Identify the Successor**

The **Successor** is the best route to the destination.

R1
 │
 │ Best route
 ▼
R2 ─────→ R3

Successor FD = 20


**Step 2: Check the Backup Route**

R4 also knows how to reach R3.

R1 asks R4:

"What is your distance to the destination?"

Suppose R4 replies:

R4's Reported Distance (RD) = 15

Now EIGRP checks the **Feasibility Condition**:

RD < Successor FD

15 < 20

This is TRUE. ✅

Therefore:

R4 = **Feasible Successor**

Now R1 has:

Successor          → R2
Feasible Successor → R4


**Step 3: Check Variance**

Suppose:

Best path FD = 20
Backup path metric = 30

And we configure:

variance 2

EIGRP calculates:

20 × 2 = 40

The backup route's metric is:

30

Now:

30 ≤ 40

So R4 can participate in **unequal-cost load balancing**. ✅

Traffic can now use:

R1 → R2 → R3    Metric = 20
R1 → R4 → R3    Metric = 30

The costs are different, so this is called **unequal-cost load balancing**.


**Step 4: What if the Feasibility Condition Fails?**

Now let's change only one thing.

Suppose R4's Reported Distance is:

RD = 25

Remember:

Successor FD = 20

Check the Feasibility Condition:

RD < FD

25 < 20

❌ FALSE

Therefore:

R4 is **NOT a Feasible Successor**.


**Step 5: What if we increase the Variance?**

Suppose we configure:

variance 10

The variance calculation would be:

20 × 10 = 200

R4's metric is only:

30

So mathematically:

30 ≤ 200

But R4 still **will NOT be used** for unequal-cost load balancing.

Why?

Because the feasibility condition failed first.

The process is:

Feasibility Condition ❌
        ↓
Not a Feasible Successor
        ↓
Cannot be used for unequal-cost load balancing

**Variance cannot fix a failed Feasibility Condition.**


**Why does EIGRP require the Feasibility Condition?**

EIGRP uses the Feasibility Condition to help ensure that the backup route is **loop-free**.

Imagine R1 chooses R4 as a backup route, but R4's path to the destination eventually goes back through R1.

That could create a routing loop:

R1 → R4
↑     ↓
└─────┘
  LOOP

So EIGRP first checks:

"Is this route safe and loop-free?"

Only if the answer is YES does EIGRP consider the route for unequal-cost load balancing.


**The complete flow**

Backup Route
     ↓
Check Feasibility Condition
     ↓
Is it a Feasible Successor?
     ↓
   YES
     ↓
Check Variance
     ↓
Is the route within the variance range?
     ↓
   YES
     ↓
Unequal-Cost Load Balancing


**The rule to remember**

Feasible Successor → Variance → Load Balancing

In simple words:

**First, EIGRP checks whether the backup route is a Feasible Successor.**

**Then, EIGRP checks whether its metric is within the configured variance.**

**Only then can the route participate in unequal-cost load balancing.**

Most important condition:

RD < Successor's FD

If:

RD ≥ Successor's FD

then:

❌ Not a Feasible Successor
❌ Cannot participate in unequal-cost load balancing
❌ Increasing the variance will NOT help

💡 EIGRP will only perform UNEQUAL-COST LOAD-BALANCING over ***feasible successor*** ROUTES. If a ROUTE doesn’t meet the ***feasibility condition***, it will NEVER be selected for load-balancing, regardless of **variance**
