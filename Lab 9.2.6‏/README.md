<div align="center">

# 🦈 Lab — Using Wireshark to Observe the TCP 3-Way Handshake

### Cisco CyberOps • TCP Analysis • Wireshark • tcpdump • Mininet

<p>
  <img src="https://img.shields.io/badge/Cisco-CyberOps-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white">
  <img src="https://img.shields.io/badge/Wireshark-Packet%20Analysis-1679A7?style=for-the-badge&logo=wireshark&logoColor=white">
  <img src="https://img.shields.io/badge/tcpdump-Packet%20Capture-blue?style=for-the-badge">
  <img src="https://img.shields.io/badge/Protocol-TCP-orange?style=for-the-badge">
  <img src="https://img.shields.io/badge/Topology-Mininet-green?style=for-the-badge">
</p>

</div>

---

## 🎯 Objectives

- Prepare Mininet hosts to capture TCP traffic.
- Start a simulated web server on H4.
- Capture traffic generated between H1 and H4.
- Analyze the **TCP three-way handshake** in Wireshark.
- Identify:
  - Source and destination IP addresses
  - TCP source and destination ports
  - TCP control flags
  - Relative sequence numbers
  - Relative acknowledgment numbers
- Examine the same capture using `tcpdump`.
- Understand how TCP establishes a reliable session before application data is exchanged.

---

## 🧩 Background

When an application such as **HTTP** or **FTP** starts communication with another host, TCP uses a **three-way handshake** to establish a reliable TCP session.

For example, when a web browser connects to a web server, TCP first establishes the session before application data can be exchanged.

A PC can maintain multiple simultaneous TCP sessions with different web servers.

### TCP Three-Way Handshake

```text
Client / H1                         Server / H4
     │                                  │
     │ ─────── SYN ──────────────────> │
     │                                  │
     │ <────── SYN + ACK ───────────── │
     │                                  │
     │ ─────── ACK ──────────────────> │
     │                                  │
     │       TCP Connection             │
     │          Established             │
     │                                  │
```

### 🛡️ Lab Note

Using a packet sniffer such as Wireshark may be considered a breach of a school's security policy. Appropriate permission should be obtained before running packet-capture tools.

---

## 🖥️ Required Resources

- **CyberOps Workstation virtual machine**

### Lab Credentials

| Item | Value |
|---|---|
| Username | `analyst` |
| Password | `cyberops` |

---

# 🧪 Part 1 — Prepare the Hosts to Capture the Traffic

In this part, the CyberOps VM is prepared with a Mininet topology containing H1 and H4. A web server is started on H4, Firefox is started on H1, and `tcpdump` is used to capture the generated traffic.

---

## 1️⃣ Start the CyberOps VM

Log into the CyberOps Workstation using:

```text
Username: analyst
Password: cyberops
```

---

## 2️⃣ Start Mininet

From the CyberOps VM terminal:

```bash
sudo lab.support.files/scripts/cyberops_topo.py
```

Enter the password when prompted:

```text
cyberops
```

---

## 3️⃣ Start H1 and H4

At the Mininet prompt:

```text
mininet> xterm H1
mininet> xterm H4
```

This opens terminal windows for the two Mininet hosts.

---

## 4️⃣ Start the Web Server on H4

From the H4 terminal:

```bash
/home/analyst/lab.support.files/scripts/reg_server_start.sh
```

H4 now acts as the simulated web server.

---

## 5️⃣ Switch H1 from Root to Analyst

Firefox cannot be run from the root account for security purposes.

On H1, switch to the `analyst` user:

```bash
su analyst
```

---

## 6️⃣ Start Firefox

From the H1 terminal:

```bash
firefox &
```

Wait for the Firefox window to open.

---

## 7️⃣ Capture Traffic with tcpdump

Start a packet capture on the H1 Ethernet interface:

```bash
sudo tcpdump -i H1-eth0 -v -c 50 -w /home/analyst/capture.pcap
```

### Command Breakdown

| Option | Purpose |
|---|---|
| `-i H1-eth0` | Capture traffic on the H1 interface |
| `-v` | Display verbose capture progress |
| `-c 50` | Stop after capturing 50 packets |
| `-w` | Write captured packets to a file |
| `capture.pcap` | Saved packet capture file |

The capture is saved as:

```text
/home/analyst/capture.pcap
```

---

## 8️⃣ Generate HTTP Traffic

Immediately after `tcpdump` starts, navigate to:

```text
172.16.0.40
```

in the Firefox browser.

This generates traffic between H1 and the web server running on H4.

---

# 🦈 Part 2 — Analyze the Packets Using Wireshark

The saved `capture.pcap` file is opened in Wireshark and filtered to examine the TCP handshake.

---

## Step 1 — Open the Capture

### 1. Start Wireshark

From the H1 terminal:

```bash
wireshark &
```

If prompted about running Wireshark as superuser, click **OK**.

---

### 2. Open the PCAP File

In Wireshark:

```text
File → Open
```

Open:

```text
/home/analyst/capture.pcap
```

---

### 3. Apply a TCP Filter

Use the Wireshark display filter:

```text
tcp
```

In the example from the lab, the first three frames contain the TCP three-way handshake.

---

# 🔍 Step 2 — Analyze the TCP Handshake

## 🟢 Frame 1 — SYN

The first frame begins the TCP three-way handshake between H1 and H4.

Expand:

```text
Transmission Control Protocol
```

Then inspect:

```text
Source Port
Destination Port
Flags
Sequence Number
```

### Example Values

| Field | Value |
|---|---|
| Source Port | `58716`* |
| Source Port Type | Dynamic / Private |
| Destination Port | `80` |
| Destination Port Type | Well-known / Registered — HTTP |
| TCP Flag | `SYN` |
| Relative Sequence Number | `0` |

> 💡 The source port may vary. `58716` is the example value provided in the lab.

### What Frame 1 Represents

```text
H1                                      H4
 │                                      │
 │──── SYN, Seq = 0 ─────────────────>│
 │                                      │
```

The client requests the establishment of a TCP session with the server.

---

# 🟡 Frame 2 — SYN + ACK

Select the second packet in the capture.

This packet is the web server's response to the initial SYN.

### Example Values

| Field | Value |
|---|---|
| Source Port | `80` |
| Destination Port | `58716`* |
| Flags | `SYN` + `ACK` |
| Relative Sequence Number | `0` |
| Relative Acknowledgment Number | `1` |

> 💡 The destination port corresponds to the dynamic source port selected by H1.

### What Frame 2 Represents

```text
H1                                      H4
 │                                      │
 │──── SYN ───────────────────────────>│
 │                                      │
 │<─── SYN + ACK ──────────────────────│
 │                                      │
```

The server acknowledges the client's SYN and sends its own SYN.

---

# 🔵 Frame 3 — ACK

Select the third and final packet of the handshake.

The packet contains:

| Field | Value |
|---|---|
| Flag | `ACK` |
| Relative Sequence Number | `1` |
| Relative Acknowledgment Number | `1` |

### What Frame 3 Represents

```text
H1                                      H4
 │                                      │
 │──── SYN ───────────────────────────>│
 │<─── SYN + ACK ──────────────────────│
 │──── ACK ───────────────────────────>│
 │                                      │
 │       TCP Connection Established     │
```

At this point, the TCP connection is established and communication between H1 and H4 can begin.

---

# 🧠 TCP Three-Way Handshake Summary

| Frame | Direction | Flags | Relative Seq. | Relative Ack. |
|---|---|---|---:|---:|
| 1 | H1 → H4 | `SYN` | `0` | — |
| 2 | H4 → H1 | `SYN + ACK` | `0` | `1` |
| 3 | H1 → H4 | `ACK` | `1` | `1` |

### Handshake Flow

```text
             TCP SESSION ESTABLISHMENT

H1 / Client                              H4 / Server
     │                                       │
     │──────── SYN (Seq=0) ─────────────────>│
     │                                       │
     │<────── SYN + ACK (Seq=0, Ack=1) ─────│
     │                                       │
     │──────── ACK (Seq=1, Ack=1) ─────────>│
     │                                       │
     │         CONNECTION ESTABLISHED        │
     │                                       │
     │──────── Application Data ────────────>│
```

---

# 🧪 Part 3 — View the Packets Using tcpdump

Wireshark is not the only tool that can analyze a PCAP file. `tcpdump` can also read saved packet captures.

---

## 1️⃣ Read the tcpdump Manual

Open the manual:

```bash
man tcpdump
```

The manual provides information about available options and packet-filtering capabilities.

### Searching the Manual

Inside `man`:

```text
/-r
```

Searches forward for `-r`.

Use:

```text
n
```

to move to the next match.

Use:

```text
q
```

to quit the manual.

---

## 2️⃣ Understand the `-r` Option

The `-r` option allows `tcpdump` to **read packets from a file** previously saved using the `-w` option or another tool that writes PCAP/PCAP-NG files.

---

## 3️⃣ Read the First Three TCP Packets

Run:

```bash
tcpdump -r /home/analyst/capture.pcap tcp -c 3
```

Example output:

```text
reading from file capture.pcap, link-type EN10MB (Ethernet)

13:58:30.647462 IP 10.0.0.11.58716 > 172.16.0.40.http:
Flags [S], seq 2432755549, win 29200,
options [mss 1460,sackOK,TS val 3864513189 ecr 0,nop,wscale 9], length 0

13:58:30.647543 IP 172.16.0.40.http > 10.0.0.11.58716:
Flags [S.], seq 1766419191, ack 2432755550, win 28960,
options [mss 1460,sackOK,TS val 50557410 ecr 3864513189,nop,wscale 9],
length 0

13:58:30.647544 IP 10.0.0.11.58716 > 172.16.0.40.http:
Flags [.], ack 1, win 58,
options [nop,nop,TS val 3864513189 ecr 50557410], length 0
```

### tcpdump Representation

| Packet | Source | Destination | Flag |
|---|---|---|---|
| 1 | `10.0.0.11:58716` | `172.16.0.40:80` | `[S]` |
| 2 | `172.16.0.40:80` | `10.0.0.11:58716` | `[S.]` |
| 3 | `10.0.0.11:58716` | `172.16.0.40:80` | `[.]` |

Where:

```text
[S]  = SYN
[S.] = SYN + ACK
[.]  = ACK
```

This provides a command-line representation of the same TCP three-way handshake observed in Wireshark.

---

# 🔄 Wireshark + tcpdump Workflow

```text
┌──────────────────────────────┐
│ Start CyberOps VM            │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Start Mininet                │
│ cyberops_topo.py             │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Start H1 + H4                │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Start Web Server on H4       │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Start Firefox on H1          │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ tcpdump → capture.pcap       │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Browse to 172.16.0.40        │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Open PCAP in Wireshark       │
│ Filter: tcp                  │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Analyze SYN                  │
│ SYN + ACK                    │
│ ACK                          │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Read PCAP with tcpdump       │
└──────────────────────────────┘
```

---

# 🧹 Cleanup

Terminate Mininet from the main CyberOps VM terminal:

```text
mininet> quit
```

The lab stops the Mininet components, including:

```text
H1 H2 H3 H4 R1
s1
```

Then clean up Mininet processes:

```bash
sudo mn -c
```

Enter:

```text
cyberops
```

when prompted for the password.

---

# 💭 Reflection Questions

## 1. Useful Wireshark Filters

The lab notes that many filters can be used depending on the traffic being investigated.

Examples include:

```text
tcp
```

Specific IP addresses:

```text
ip.addr == 10.0.0.11
```

HTTP traffic:

```text
http
```

These filters can help a network administrator isolate specific traffic from a larger capture.

---

## 2. Other Uses of Wireshark in a Production Network

According to the lab, Wireshark can be used for:

- After-the-fact analysis of normal network traffic.
- Analysis following a network attack.
- Investigating new protocols or services.
- Determining which ports are being used by protocols or services.
- Network troubleshooting and traffic analysis.

---

# 🛡️ Cybersecurity Relevance

Understanding TCP session establishment is important for network monitoring and security analysis.

### 🔎 SOC & Network Analysis

A security analyst can inspect TCP packets to identify:

- Connection attempts
- Source and destination hosts
- Source and destination ports
- TCP control flags
- Sequence and acknowledgment behavior
- Repeated connection attempts
- Unexpected services or ports

### 🚨 Incident Response

PCAP analysis can help reconstruct network communication after a suspicious event. Examining TCP handshakes provides an initial view of which hosts attempted to establish sessions and which services were targeted.

### 🌐 Network Security

The distinction between dynamic client ports and well-known service ports is useful when interpreting network traffic.

In this lab:

```text
H1: Dynamic Source Port
        │
        │ TCP
        ▼
H4: Port 80 / HTTP
```

---

# 📌 Key Takeaways

> 🦈 **Wireshark** can display TCP packets and expose their protocol fields, including ports, flags, and sequence numbers.

> 🤝 **TCP uses a three-way handshake** to establish a reliable session.

> `SYN → SYN/ACK → ACK` represents the three steps of the handshake.

> 🔢 **Relative sequence and acknowledgment numbers** help track TCP communication.

> 🔬 **tcpdump** can read saved PCAP files and provide a command-line view of captured TCP traffic.

> 🌐 **Port 80** is used by the simulated HTTP web server in this lab, while H1 uses a dynamic/private source port.

---

## 🧰 Commands Used

```bash
# Start Mininet
sudo lab.support.files/scripts/cyberops_topo.py

# Start the web server on H4
/home/analyst/lab.support.files/scripts/reg_server_start.sh

# Switch user
su analyst

# Start Firefox
firefox &

# Capture traffic
sudo tcpdump -i H1-eth0 -v -c 50 -w /home/analyst/capture.pcap

# Start Wireshark
wireshark &

# Open tcpdump manual
man tcpdump

# Read the first three TCP packets
tcpdump -r /home/analyst/capture.pcap tcp -c 3

# Clean up Mininet
sudo mn -c
```

### Wireshark Display Filter

```text
tcp
```

---

<div align="center">

### 🦈 Cisco CyberOps • TCP • Wireshark • tcpdump

**TCP Session Analysis | Packet Capture | Network Security**

</div>
