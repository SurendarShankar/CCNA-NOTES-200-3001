# 18. VLANS : PART 3

NATIVE VLAN ON A ROUTER (ROAS)

![image](https://github.com/psaumur/CCNA/assets/106411237/838b9835-d17d-4d57-bac1-52f7e3adfd77)

Native VLAN untagged frames are faster and more efficient (smaller) than tagged ones.

Let’s reset all SWITCHES (SW1 and SW2) to native vlan 10

![image](https://github.com/psaumur/CCNA/assets/106411237/1e903c1b-b814-40b5-aaea-1ba9f3f192c8)



There are **TWO methods** of configuring the native VLAN on a router:

- Use the command “encapsulation dot1q <vlan-id>” on a Sub-Interface

![image](https://github.com/psaumur/CCNA/assets/106411237/2ea65208-6b2a-4cac-a463-982a731c9e24)


OR

- Configure the IP address for the native VLAN on the router’s physical interface (the “**encapsulation dot1q** <vlan-id> command is not necessary”

![image](https://github.com/psaumur/CCNA/assets/106411237/dabcc3b4-13c3-4d60-abe2-c7cbb5edd4c2)


Output of “show running-config” of G0/0 Interface

![image](https://github.com/psaumur/CCNA/assets/106411237/37ce4f0f-0ac0-45ce-802f-5fd11057f69d)


---

LAYER 3 (MULTILAYER) SWITCHES

ICON APPEARANCE

![image](https://github.com/psaumur/CCNA/assets/106411237/0d63f5f9-5efe-4c61-a8e6-3cd6a1161d2a)


- A MULTILAYER SWITCH is capable of both SWITCHING and ROUTING
- It is LAYER 3 AWARE
- You can assign IP Addresses to its L3 Virtual Interface, like a router
- You can create Virtual Interfaces for each VLAN, and assign IP addresses to those interfaces
- You can configure routes on it, just like a ROUTER
- It can be used for inter-VLAN routing

![image](https://github.com/psaumur/CCNA/assets/106411237/af59481b-d0cb-41d7-9eba-7c8d47131c28)


SW2 Replaced with a Layer 3 Switch

Multi-VLAN connections to R1 removed and replaced with a point-to-point Layer 3 connection

![image](https://github.com/psaumur/CCNA/assets/106411237/8f3ad167-d774-4fcb-96a5-66e568edead8)


- SVIs (Switch Virtual Interfaces) are the virtual interfaces you can assign IP addresses to in a MULTILAYER SWITCH.
- Configure each HOST to use the SVI (NOT the ROUTER R1) as their Gateway Address
- To send traffic to different SUBNETS / VLANS, the PCs will send traffic to the SWITCH, and the SWITCH will route the traffic.

![image](https://github.com/psaumur/CCNA/assets/106411237/5409b2cc-f876-4754-afe3-33298930fd7a)


![image](https://github.com/psaumur/CCNA/assets/106411237/953372de-579a-4803-9418-0bd1aeef229d)


Clearing R1 configuration to set to work with the Layer 3 Point-to-Point connection

![image](https://github.com/psaumur/CCNA/assets/106411237/40354cbe-df39-4a78-97cd-bbb0bc10549c)


#no interface <sub-interface id> : removes the VLAN interface

#default interface g0/0 : resets the g0/0 interface to it’s default settings

Then configure the default R1 G0/0 interface’s to IP address : 192.168.1.194 (as per the network diagram)

Configuration of SW2 to use SVI and the Layer 3 Point-to-Point connection with R1

![image](https://github.com/psaumur/CCNA/assets/106411237/24d64087-f98c-4a1e-a07f-3f93f06f93a9)


“default interface <interface-id>” : resets settings on specified interface to defaults

“ip routing” : **IMPORTANT** command to enable Layer 3 routing on the SWITCH

“no switchport” : configures the interface from a Layer 2 Switchport to a Layer 3 “routed port” 

The sets the Default Route to R1 (192.168.1.194) so that all traffic leaving the network gets routed through R1’s Gateway of Last Resort (aka The Default Gateway)

![image](https://github.com/psaumur/CCNA/assets/106411237/7a682a2f-3ae3-420b-8f68-9e1050dd82c6)


![image](https://github.com/psaumur/CCNA/assets/106411237/c0b544b7-8f32-49ae-9a46-d09390a3d08c)


SVI CONFIGURATION ON SW2 (Virtual LAYER 3 ROUTING INTERFACES)

![image](https://github.com/psaumur/CCNA/assets/106411237/7c1710fb-40d7-44a4-8336-b037e1c2ea77)



SVIs are **shut down** by default, so remember to use “no shutdown”

![image](https://github.com/psaumur/CCNA/assets/106411237/2b5b13c3-1364-4296-886c-0bd9b00b4167)


Creating an unknown SVI (VLAN 40) and the Status/Protocol is “down/down”

What are the conditions for a SVI to be “up/up” ? 

- The VLAN must exist on the SWITCH
- The SWITCH must have at least ONE access port in the VLAN in an “up/up” state AND/OR one TRUNK port that allows the VLAN that is in an “up/up” state
- The VLAN must not be shutdown (you can use the “shutdown” command to disable a VLAN)
- The SVI must not be shutdown (SVIs are disabled, by default)

![image](https://github.com/psaumur/CCNA/assets/106411237/558ef418-5902-42d0-b4a5-cce14b56b77e)


The VLAN trunk has been successfully replaced by an Layer 3 SWITCH SVI.

All hosts should be able to connect with each other (tested with “ping”) as well as reach the external internet (via the Cloud symbol attached to R1)

---
- EXTRA :
# SVI (Switch Virtual Interface) - Conditions to Become UP/UP

## 💡 Easy Analogy

Think of the **SVI (`interface vlan 10`)** as a **light bulb**.

For the light bulb to turn **ON (UP/UP)**, **all four switches must be ON**.

```text
          SVI (VLAN 10)
               💡
                |
     -------------------------
     |     |      |         |
     ✓     ✓      ✓         ✓
    (1)   (2)    (3)       (4)
```

If **any one condition fails**, the SVI will remain **DOWN**.

---

# Condition 1: The VLAN Must Exist

The switch must already have the VLAN created.

❌ Incorrect

```bash
interface vlan 10
```

Only the SVI is created. **VLAN 10 does not exist yet.**

✅ Correct

```bash
vlan 10
```

Now VLAN 10 exists.

### Analogy

Think of VLAN 10 as a **room**.

If the room doesn't exist, you can't turn on its light.

---

# Condition 2: At Least One Port in the VLAN Must Be UP

The switch must have **at least one active interface** in VLAN 10.

This can be:

- An **access port** assigned to VLAN 10, or
- A **trunk port** carrying VLAN 10.

---

## Example 1: Access Port

```text
        PC
         |
      Fa0/1
         |
      +--------+
      | Switch |
      +--------+
```

If the cable is connected:

```text
PC
 |
Fa0/1  ✅ UP
 |
Switch
```

The switch knows:

> "Someone is using VLAN 10."

Therefore:

```text
SVI = UP
```

---

If the cable is unplugged:

```text
PC

Fa0/1 ❌ DOWN
 |
Switch
```

The switch thinks:

> "Nobody is using VLAN 10."

Therefore:

```text
SVI = DOWN
```

---

## Example 2: Trunk Port

Even if no PC is connected, a trunk carrying VLAN 10 also keeps the VLAN active.

```text
+----------+====================+----------+
| Switch A |      Trunk         | Switch B |
+----------+====================+----------+

Allowed VLANs: 10,20,30
```

Since VLAN 10 is active on the trunk:

```text
SVI = UP
```

---

# Condition 3: The VLAN Must NOT Be Shutdown

The VLAN itself must be enabled.

If VLAN 10 is administratively shut down:

```text
VLAN 10
🔒 Disabled
```

Then:

```text
SVI = DOWN
```

### Analogy

Imagine locking an entire building.

Even though the lights are installed, nobody can enter.

---

# Condition 4: The SVI Must NOT Be Shutdown

By default, the SVI is administratively down.

Enable it with:

```bash
interface vlan 10
no shutdown
```

Without `no shutdown`

```text
SVI
❌ Disabled
```

With `no shutdown`

```text
SVI
✅ Enabled
```

---

# Summary

```text
                     SVI (VLAN 10)
                          💡
                           |
        -----------------------------------------
        |                |          |           |
        |                |          |           |
 VLAN Exists     Active Port     VLAN        SVI
                 (Access/Trunk) Enabled     Enabled
        |                |          |           |
        ✓                ✓          ✓           ✓
                           |
                           v
                    SVI = UP / UP
```

---

# CCNA Memory Trick

Remember these **4 checks**:

✅ VLAN exists

✅ At least one active access port **or** trunk carrying that VLAN

✅ VLAN is not shut down

✅ SVI has `no shutdown`

If **all four are true**:

```text
SVI = UP / UP ✅
```

If **any one is false**:

```text
SVI = DOWN ❌
```

---

# One-Line CCNA Exam Definition

> **An SVI becomes UP/UP only when the VLAN exists, the VLAN is enabled, the SVI is enabled, and at least one active access port or trunk carrying that VLAN is in the UP state.**

---
