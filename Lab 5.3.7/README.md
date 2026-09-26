<div align="center">

# 🦈 Lab — Introduction to Wireshark Mininet Topology

### Cisco CyberOps • Network Traffic Analysis • Mininet • Wireshark

<p>
  <img src="https://img.shields.io/badge/Cisco-CyberOps-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white">
  <img src="https://img.shields.io/badge/Wireshark-Packet%20Analysis-1679A7?style=for-the-badge&logo=wireshark&logoColor=white">
  <img src="https://img.shields.io/badge/Mininet-Network%20Emulation-orange?style=for-the-badge">
  <img src="https://img.shields.io/badge/Protocol-ICMP-blue?style=for-the-badge">
</p>

</div>

---

## 🎯 Objectives

- Install and verify the **Mininet topology** inside the CyberOps Workstation VM.
- Record the **IP and MAC addresses** of Mininet hosts.
- Capture **ICMP traffic** using Wireshark.
- Analyze ICMP requests and replies on the **same LAN**.
- Compare local-LAN traffic with traffic destined for a **remote LAN**.
- Identify how **Ethernet, IPv4, and ICMP** information appears inside captured packets.

---

## 🧩 Background

The CyberOps VM includes a Python script that creates and configures a simulated network containing:

- **Four hosts**
- **One switch**
- **One router**

This Mininet topology allows network protocols and services to be simulated inside a single VM.

In this lab, ICMP traffic is generated using the `ping` command and then captured and analyzed with **Wireshark**.

### 🦈 What is Wireshark?

Wireshark is a software protocol analyzer, or **packet sniffer**, used for:

- Network troubleshooting
- Traffic analysis
- Software and protocol development
- Education

As data streams travel across a network, Wireshark captures protocol data units (PDUs) and decodes their contents according to the appropriate RFCs or other specifications.

> ⚠️ **Lab Note:** Using a packet sniffer may be considered a breach of an organization's or school's security policy. Obtain appropriate permission before capturing network traffic.

---

## 🖥️ Required Resources

- **CyberOps Workstation virtual machine**

### Lab Credentials

| Item | Value |
|---|---|
| Username | `analyst` |
| Password | `cyberops` |

---

# 🧪 Part 1 — Install and Verify the Mininet Topology

In this part, the Mininet topology is created inside the CyberOps VM. The IP and MAC addresses of selected hosts are then recorded.

---

## 1️⃣ Start the CyberOps Workstation

Log into the CyberOps Workstation using:

```text
Username: analyst
Password: cyberops
```

---

## 2️⃣ Start the Mininet Topology

Open a terminal and run:

```bash
sudo ~/lab.support.files/scripts/cyberops_topo.py
```

When prompted for the password, enter:

```text
cyberops
```

The command starts the Mininet environment and creates the simulated network.

### Mininet Topology

```text
        H1 ──┐
        H2 ──┤
        H3 ──┤── Switch ── R1 ── H4
             │
```

The topology provides separate hosts and router interfaces that can be used to generate and inspect network traffic.

---

## 3️⃣ Record H1 and H2 Addresses

At the Mininet prompt, open terminal windows for H1 and H2:

```text
mininet> xterm H1
mininet> xterm H2
```

On each host, run:

```bash
ip address
```

Example H1 output:

```text
2: H1-eth0@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP
group default qlen 1000
link/ether ba:d4:1d:7b:f3:61 brd ff:ff:ff:ff:ff:ff link-netnsid 0
inet 10.0.0.11/24 brd 10.0.0.255 scope global H1-eth0
valid_lft forever preferred_lft forever
inet6 fe80::b8d4:1dff:fe7b:f361/64 scope link
```

### Addressing Information

| Host Interface | IP Address | MAC Address |
|---|---|---|
| `H1-eth0` | `10.0.0.11` | `ba:d4:1d:7b:f3:61`* |
| `H2-eth0` | `10.0.0.12` | `4e:b8:9c:5a:aa:50`* |

> 💡 **Note:** MAC addresses may vary between lab executions. The addresses above are examples from the Cisco lab.

---

# 🦈 Part 2 — Capture and Analyze ICMP Data in Wireshark

In this part, ICMP traffic is generated between Mininet hosts and captured with Wireshark.

The analysis demonstrates how packet headers identify the source and destination and how data is encapsulated across protocol layers.

---

## 📡 Step 1 — Examine Captured Data on the Same LAN

### 1. Start Wireshark on H1

From the H1 terminal:

```bash
wireshark &
```

The lab may display a warning dialog. Click **OK** to continue.

---

### 2. Select the Capture Interface

In Wireshark:

1. Locate the **Capture** section.
2. Select the `H1-eth0` interface.
3. Click **Start**.

Wireshark will begin capturing traffic on H1's interface.

---

### 3. Generate ICMP Traffic

From the H1 terminal, ping H2 five times:

```bash
ping -c 5 10.0.0.12
```

The `-c` option specifies the number of ICMP requests to send.

Expected behavior:

```text
H1  ───── ICMP Echo Request ─────>  H2
H1  <──── ICMP Echo Reply ───────  H2
```

All five pings should be successful.

---

### 4. Stop the Capture

Return to Wireshark and click:

**Stop**

---

### 5. Filter for ICMP

Enter the following display filter:

```text
icmp
```

Click **Apply**.

This limits the displayed packets to ICMP traffic.

---

## 🔍 Analyze an ICMP Request

Select the first ICMP request in the packet list.

The top section of Wireshark displays the captured PDU frames and summary information.

The middle section displays the protocol layers for the selected frame.

The bottom section displays the raw packet data in hexadecimal and decimal form.

### Wireshark Packet Structure

```text
┌─────────────────────────────┐
│ Ethernet II                 │
│ Source MAC                  │
│ Destination MAC             │
├─────────────────────────────┤
│ IPv4                        │
│ Source IP                   │
│ Destination IP             │
├─────────────────────────────┤
│ ICMP                        │
│ Echo Request / Reply        │
└─────────────────────────────┘
```

---

## 🧠 Verify the Ethernet Information

Expand the **Ethernet II** section in Wireshark.

For an ICMP request from H1 to H2:

| Field | Expected Value |
|---|---|
| Source IP | H1 → `10.0.0.11` |
| Destination IP | H2 → `10.0.0.12` |
| Source MAC | H1's interface MAC |
| Destination MAC | H2's interface MAC |

### Lab Questions

**Does the Source MAC address match H1's interface?**

> **Yes.**

**Does the Destination MAC address in Wireshark match H2's MAC address?**

> **Yes.**

### Encapsulation

The captured ICMP request demonstrates protocol encapsulation:

```text
ICMP Data
   ↓
IPv4 Packet
   ↓
Ethernet II Frame
   ↓
LAN Transmission
```

The ICMP data is encapsulated inside an IPv4 packet, which is then encapsulated inside an Ethernet II frame for transmission across the LAN.

---

# 🌐 Step 2 — Examine Captured Data on the Remote LAN

The next step examines traffic sent from H1 to H4, which is located on a different network.

---

## 1. Open H4 and R1 Terminals

At the Mininet prompt:

```text
mininet> xterm H4
mininet> xterm R1
```

---

## 2. Record H4 and R1 Addresses

On H4:

```bash
ip address
```

On R1:

```bash
ip address
```

Record the relevant interfaces.

| Host Interface | IP Address | MAC Address |
|---|---|---|
| `H4-eth0` | `172.16.0.40` | Answers may vary |
| `R1-eth1` | `10.0.0.1` | Answers may vary |
| `R1-eth2` | `172.16.0.1` | Answers may vary |

---

## 3. Start a New Wireshark Capture

On H1, start a new capture:

**Capture → Start**

Alternatively:

- Click **Start**
- Press `Ctrl-E`

If prompted, select:

**Continue without Saving**

---

## 4. Ping the Remote Host

From H1, ping H4:

```bash
ping -c 5 172.16.0.40
```

The ping should be successful.

---

## 🔬 Analyze the Remote-LAN Traffic

Review the captured traffic in Wireshark.

The important observation is that the **destination IP address remains the remote host's IP address**, while the Ethernet destination MAC address is the MAC address of the router interface serving as the default gateway.

### Expected Information

| Field | Value |
|---|---|
| Destination IP | `172.16.0.40` |
| Destination MAC | MAC address associated with `R1-eth1` |

`R1-eth1` is the default gateway for hosts **H1, H2, and H3** on the `10.0.0.0/24` LAN.

### Local vs Remote LAN

```text
SAME LAN
────────

H1
10.0.0.11
   │
   │ Destination IP: 10.0.0.12
   │ Destination MAC: H2
   ▼
H2


REMOTE LAN
──────────

H1
10.0.0.11
   │
   │ Destination IP: 172.16.0.40
   │ Destination MAC: R1-eth1
   ▼
R1
   │
   │ Routes packet toward remote LAN
   ▼
H4
172.16.0.40
```

### 🔑 Key Observation

When the destination host is on the **same LAN**, the Ethernet destination MAC corresponds to the destination host.

When the destination host is on a **remote LAN**, the Ethernet destination MAC corresponds to the router interface used as the default gateway.

The destination **IP address remains the remote host's IP address**.

---

# 🧹 Cleanup

After completing the packet analysis, stop Mininet from the main CyberOps VM terminal:

```text
mininet> quit
```

The lab should stop the Mininet controllers, terminals, links, switch, and hosts.

Then clean up remaining Mininet processes:

```bash
sudo mn -c
```

Enter the `analyst` user's password when prompted:

```text
cyberops
```

A successful cleanup ends with:

```text
*** Cleanup complete.
```

---

# 🔄 Lab Workflow

```text
┌──────────────────────────────┐
│ CyberOps Workstation         │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Start Mininet Topology       │
│ cyberops_topo.py              │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Record IP & MAC Addresses    │
│ H1 / H2 / H4 / R1            │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Start Wireshark              │
│ Capture on H1-eth0           │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Generate ICMP Traffic        │
│ ping -c 5 <destination>      │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Filter: icmp                 │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Analyze Ethernet + IPv4 +    │
│ ICMP headers                 │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Compare Same LAN vs          │
│ Remote LAN Traffic           │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Stop & Clean Mininet         │
│ sudo mn -c                   │
└──────────────────────────────┘
```

---

# 🛡️ Cybersecurity Relevance

This lab demonstrates several fundamental concepts used in network security and SOC operations:

- **Packet capture** — observing network traffic for analysis.
- **Wireshark filtering** — isolating relevant protocol traffic.
- **ICMP analysis** — identifying Echo Requests and Echo Replies.
- **MAC address analysis** — understanding Layer 2 delivery.
- **IP address analysis** — identifying Layer 3 source and destination information.
- **Network segmentation** — distinguishing local and remote networks.
- **Default gateway behavior** — understanding how traffic reaches remote destinations.
- **Protocol encapsulation** — seeing how ICMP is transported through IPv4 and Ethernet.

These concepts are useful when investigating network anomalies, validating connectivity, troubleshooting routing behavior, and analyzing packet captures during security investigations.

---

# 📌 Key Takeaways

> 🦈 **Wireshark** provides visibility into captured network PDUs and their protocol layers.

> 📡 **ICMP** can be generated with `ping` and filtered in Wireshark using `icmp`.

> 🔗 **Same-LAN traffic** uses the destination host's MAC address at the Ethernet layer.

> 🌐 **Remote-LAN traffic** uses the router's MAC address as the Ethernet destination while retaining the remote host's IP address as the Layer 3 destination.

> 🧩 **Encapsulation** allows higher-layer protocols such as ICMP to be transported inside IPv4 packets and Ethernet frames.

---

## 🧰 Commands Used

```bash
# Start the Mininet topology
sudo ~/lab.support.files/scripts/cyberops_topo.py

# Display interface information
ip address

# Start Wireshark
wireshark &

# Ping H2
ping -c 5 10.0.0.12

# Ping remote H4
ping -c 5 172.16.0.40

# Clean up Mininet
sudo mn -c
```

### Wireshark Filter

```text
icmp
```

---

<div align="center">

### 🦈 Cisco CyberOps • Mininet • Wireshark • ICMP

**Network Traffic Analysis | Packet Capture | Protocol Inspection**

</div>
