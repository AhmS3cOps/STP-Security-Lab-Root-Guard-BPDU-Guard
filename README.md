# **STP Security Lab: Root Guard & BPDU Guard**

## **Description**  
This project demonstrates how to secure a switched network in Cisco Packet Tracer using **Spanning Tree Protocol (STP)** features: **Root Guard** and **BPDU Guard**. These mechanisms prevent rogue switches from becoming the root bridge and disrupting network topology.

---

## **Languages and Utilities Used**
- **Cisco Packet Tracer**: Network simulation tool.
- **CLI (Command Line Interface)**: For configuring network devices.
- **STP (Spanning Tree Protocol)**: For loop prevention and topology management.

## **Environments Used**
- **Windows 11**: Operating system used to run Packet Tracer.

---

## **Program Walk-through**

### **Initial Setup**

The base topology includes:
- S2: Root Bridge (priority: 4096)
- S1: Backup Root Bridge (priority: 8192)
- S3: Access switch connected to PC3
- PC1, PC2, PC3: End devices on access VLAN 1
- A rogue switch labeled **Attacker**

<p align="center">
  <img src="https://imgur.com/LxpmQ8C.png" width="70%" alt="Attacker Priority Config"/>
</p>
---

### **Root Guard Implementation**

Root Guard prevents a non-root switch from influencing STP if it's connected to a port where the root bridge shouldn't change.
Lab 1: Root Guard Implementation
Task List 1 — Simulate the Attack
Configure the Attacker switch with a root priority of 0 for VLAN 1

Connect the Attacker to the network:

G1/0/1 → S2

G1/0/2 → S3

Verify the Attacker becomes the root bridge

Ping PC1 and PC2 to PC3 — confirm traffic flows through the Attacker

Observe which interfaces are blocked on S3

Task List 2 — Enable Root Guard
Remove the link from Attacker to S2 and S3

Enable Root Guard on:

S2 ports: G1/0/1 → G1/0/24

S3 & S1 ports: G1/0/3 → G1/0/24

Reconnect Attacker to S2 and S3

Verify Root Guard blocks the Attacker

Confirm S2 remains the root



#### **1. Attacker Switch Configuration**

```plaintext
spanning-tree vlan 1 priority 0
```

<p align="center">
  <img src="https://imgur.com/6e0ir2G.png" width="70%" alt="Attacker Priority Config"/>
</p>

#### **2. Attacker Connection to Network**

Attacker is connected to S2 (G1/0/1) and S3 (G1/0/2):

<p align="center">
  <img src="https://imgur.com/ihr0Bmf.png" width="70%" alt="Topology with Attacker Connected"/>
</p>

#### **3. Attacker Becomes Root**

<p align="center">
  <img src="https://imgur.com/885Mkb3.png" width="70%" alt="Attacker Becomes Root"/>
</p>

---

#### **5. Enable Root Guard on S2, S3**

**S2:**

```plaintext
interface range g1/0/1 - 24
spanning-tree guard root
```

<p align="center">
  <img src="https://imgur.com/LBhoiYt.png" width="70%" alt="Enable Root Guard on S2"/>
</p>

**S3:**

```plaintext
interface range g1/0/3 - 24
spanning-tree guard root
```

<p align="center">
  <img src="https://imgur.com/W9zs85W.png" width="70%" alt="Enable Root Guard on S3"/>
</p>

---

#### **6. Root Guard Blocks Attacker**

Port enters **root-inconsistent** state on S2 and S3:

<p align="center">
  <img src="https://imgur.com/50sr6rJ.png" width="70%" alt="Root Guard Triggered on S3"/>
</p>

<p align="center">
  <img src="https://imgur.com/b7p9KhR.png" width="70%" alt="Root Guard Triggered on S2"/>
</p>

---

#### **7. Verify S2 is Still Root**

```plaintext
show spanning-tree
```

<p align="center">
  <img src="https://imgur.com/ttbTXGj.png" width="70%" alt="S2 Remains Root"/>
</p>

---

### **BPDU Guard Implementation**

BPDU Guard disables access ports if they receive BPDUs, preventing rogue switches from participating in STP.

---
Task List 1 — Simulate the Attack
Configure Attacker switch with a spanning tree priority of 0 for VLAN 1

Disconnect the PC on S2 G1/0/3

Connect Attacker switch to S2 G1/0/3

Observe that the Attacker becomes the root switch

Task List 2 — Enable BPDU Guard
Disconnect the Attacker from S2

Enable BPDU Guard on S2 G1/0/3

Reconnect the Attacker

Verify that BPDU Guard disables the port

Confirm S2 remains the root

### **Initial Setup**

<p align="center">
  <img src="https://imgur.com/fcI4eW6.png" width="70%" alt="Attacker Priority Config"/>
</p>
---
#### **1. Attacker Switch Config**

```plaintext
spanning-tree vlan 1 priority 0
```

<p align="center">
  <img src="https://imgur.com/XGjBxeH.png" width="70%" alt="Attacker Configures Priority 0"/>
</p>

#### **2. Replace PC with Attacker on S2**

Attacker is connected to S2’s **G1/0/3** (an access port):

<p align="center">
  <img src="https://imgur.com/gJ3cwuT.png" width="70%" alt="Attacker Connected to S2 Access Port"/>
  <img src="https://imgur.com/pQ9GVEa.png" width="70%" alt="Attacker Connected to S2 Access Port"/>
</p>

---

#### **3. Attacker Becomes Root**

<p align="center">
  <img src="https://imgur.com/GSbLUef.png" width="70%" alt="Attacker Is Root Again"/>
</p>

---

#### **4. Enable BPDU Guard on Access Port**

```plaintext
interface GigabitEthernet1/0/3
spanning-tree bpduguard enable
```

<p align="center">
  <img src="https://imgur.com/iyLWm5T.png" width="70%" alt="Enable BPDU Guard"/>
</p>

Verify that its BDPU guard has been enabled

<p align="center">
  <img src="https://imgur.com/m4vQKxw.png" width="50%" alt="Full Config on Access Port"/>
</p>

---

#### **5. BPDU Guard Triggers**

The port shuts down upon BPDU detection:

<p align="center">
  <img src="https://imgur.com/rBMlYza.png" width="75%" alt="Port Shutdown Due to BPDU"/>
  <img src="https://imgur.com/hlzcGBK.png" width="75%" alt="Port Shutdown Due to BPDU"/>
</p>

---

#### **6. Verify S2 Remains the Root**

<p align="center">
  <img src="https://imgur.com/oHsRyQS.png" width="70%" alt="S2 is Still Root"/>
</p>
---

## ✅ **Conclusion**

This lab successfully demonstrates how:
- **Root Guard** prevents unauthorized root bridge elections on designated ports.
- **BPDU Guard** protects access ports from rogue devices sending BPDUs.

With both features configured, the network is secure against STP attacks and maintains a stable, predictable topology.
