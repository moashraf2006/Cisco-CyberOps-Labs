<div align="center">

# 🔎 Lab - Exploring DNS Traffic

**Cisco Networking Academy | Wireshark | DNS Traffic Analysis**

<p>
  <img src="https://img.shields.io/badge/Networking-DNS-blue?style=for-the-badge" alt="DNS">
  <img src="https://img.shields.io/badge/Tool-Wireshark-1679A7?style=for-the-badge" alt="Wireshark">
  <img src="https://img.shields.io/badge/Cisco-NetAcad-1BA0D7?style=for-the-badge" alt="Cisco NetAcad">
</p>

<p>
  Capture, filter, and analyze DNS query and response traffic using Wireshark.
</p>

</div>

---

## 🎯 Objectives

| Part | Objective |
|---|---|
| **Part 1** | Capture DNS Traffic |
| **Part 2** | Explore DNS Query Traffic |
| **Part 3** | Explore DNS Response Traffic |

---

## 🧰 Required Resources

- 💻 1 PC with internet access
- 🦈 Wireshark installed
- 🌐 Internet connection

> ⚠️ **Security Notice:** Using a packet sniffer such as Wireshark may be restricted by an organization's or school's security policy. Obtain permission before capturing network traffic.

---

# 🦈 Part 1 — Capture DNS Traffic

## 1. Install Wireshark

Download and install the latest stable version of Wireshark appropriate for the PC's operating system and architecture.

> **Note:** If the installer asks to install **USBPcap**, do not install it for normal traffic capture.

## 2. Start a Capture

1. Open Wireshark.
2. Select an active network interface.
3. Clear the DNS cache.

### Windows

```cmd
ipconfig /flushdns
```

### Linux

Check which DNS caching service is being used:

```bash
systemctl status systemd-resolved.service
systemctl status dnsmasq.service
systemctl status nscd.service
```

For `systemd-resolved`:

```bash
systemd-resolve --flush-caches
sudo systemctl restart systemd-resolved.service
```

For DNSMasq:

```bash
sudo systemctl restart dnsmasq.service
```

For NSCD:

```bash
sudo systemctl restart nscd.service
```

### macOS

```bash
sudo killall -HUP mDNSResponder
```

## 3. Generate DNS Traffic

Start the interactive `nslookup` utility:

```bash
nslookup
```

Query a domain:

```text
www.cisco.com
```

Exit when finished:

```text
exit
```

Stop the Wireshark capture.

---

# 🌐 Part 2 — Explore DNS Query Traffic

## 1. Filter DNS Packets

Use the following Wireshark display filter:

```text
udp.port == 53
```

This filters the capture to traffic associated with DNS.

## 2. Select the DNS Query

Find the packet containing:

```text
Standard query and A www.cisco.com
```

The **Packet Details** pane should contain:

```text
Ethernet II
Internet Protocol Version 4
User Datagram Protocol
Domain Name System (query)
```

---

## 🧩 3. Analyze Ethernet II

Expand **Ethernet II** and examine:

- Source MAC address
- Destination MAC address

In the lab example:

| Address | Associated Interface |
|---|---|
| Source MAC | PC network interface card (NIC) |
| Destination MAC | Default gateway |

If a local DNS server is being used, the destination MAC address may belong to the local DNS server.

---

## 🌍 4. Analyze IPv4

Expand **Internet Protocol Version 4**.

| Field | Meaning |
|---|---|
| Source IP | IP address associated with the PC's NIC |
| Destination IP | IP address associated with the default gateway |

---

## 🔌 5. Analyze UDP

Expand **User Datagram Protocol**.

The lab example shows:

| Field | Value |
|---|---:|
| Source Port | `577729` |
| Destination Port | `53` |
| Default DNS Port | `53` |

> 💡 DNS uses port **53** in this lab's traffic analysis.

---

## 🖥️ 6. Verify the PC's Network Information

### Windows

```cmd
arp -a
ipconfig /all
```

### Linux / macOS

```bash
ifconfig
```

or:

```bash
ip address
```

Compare the MAC and IP addresses reported by the operating system with the addresses captured by Wireshark.

**Observation:** The lab indicates that the IP and MAC addresses captured in Wireshark match the addresses listed by the system network configuration commands.

---

## 🧠 7. Analyze the DNS Query

Expand:

```text
Domain Name System (query)
```

Then expand:

```text
Flags
Queries
```

The lab identifies the recursive-query flag as being set to request the IP address associated with:

```text
www.cisco.com
```

---

# 📡 Part 3 — Explore DNS Response Traffic

## 1. Select the DNS Response

Find the corresponding packet containing:

```text
Standard query response and A www.cisco.com
```

in the **Info** column.

---

## 🔄 2. Compare Query and Response

The DNS response reverses the addressing information from the query.

| Parameter | DNS Query | DNS Response |
|---|---|---|
| Source IP | Query source | Query destination |
| Destination IP | Query destination | Query source |
| Source MAC | Query source | Query destination |
| Destination MAC | Query destination | Query source |
| Source Port | Query source port | Query destination port |
| Destination Port | Query destination port | Query source port |

This relationship helps identify the request/response flow between the client and DNS server.

---

## 🧬 3. Analyze the DNS Response

Expand:

```text
Domain Name System (response)
```

Then examine:

```text
Flags
Queries
Answers
```

The lab indicates that the DNS server can handle recursive queries.

---

## 🔗 4. Examine CNAME and A Records

Review the **CNAME** and **A** records under the **Answers** section.

Compare the information displayed by Wireshark with the output from:

```bash
nslookup
```

The lab indicates that the results shown in Wireshark should match the results returned by `nslookup`.

---

# 🧪 Reflection

## 1. What can you learn when the filter is removed?

Removing the DNS filter allows additional packets to be examined, including protocols such as:

- **DHCP**
- **ARP**

These packets can provide information about other devices and their functions within the LAN.

---

## 2. How can Wireshark be used to compromise network security?

An attacker located on the LAN could use Wireshark to observe network traffic and inspect packet details.

If traffic is **not encrypted**, sensitive information may be exposed through captured packets.

> 🔐 **Security takeaway:** Packet visibility can reveal valuable network information when communications are not adequately protected.

---

# 🧠 Key Takeaways

- 🦈 Wireshark can capture and inspect network packets.
- 🔎 DNS traffic can be filtered with `udp.port == 53`.
- 🌐 DNS query packets contain Ethernet, IPv4, UDP, and DNS information.
- 🔄 DNS responses reverse the source and destination addressing information from the query.
- 🔗 DNS responses can contain **CNAME** and **A** records.
- 🧪 `nslookup` results can be compared with captured DNS information.
- 📡 Removing filters can reveal protocols such as DHCP and ARP.
- 🔐 Unencrypted network traffic can expose sensitive information.

---

# 🛡️ Cybersecurity Relevance

Understanding DNS traffic is important for:

- Network troubleshooting
- Packet analysis
- Network protocol analysis
- Security investigations
- Reconnaissance awareness
- Identifying potentially exposed information

Wireshark provides visibility into packet-level communication, making it a valuable tool for understanding how network protocols operate and how network traffic can expose information.

---

<div align="center">

### 🚀 Cisco Networking Academy Lab

**DNS • Wireshark • Packet Analysis • Network Security**

</div>
