<div align="center">

# 🔎 Lab - Examining Telnet and SSH in Wireshark

### Cisco CyberOps Workstation | Network Traffic Analysis | Telnet vs SSH

<p>
  <img src="https://img.shields.io/badge/Cisco-CyberOps-blue?style=for-the-badge&logo=cisco" alt="Cisco CyberOps">
  <img src="https://img.shields.io/badge/Wireshark-Network%20Analysis-1679A7?style=for-the-badge&logo=wireshark" alt="Wireshark">
  <img src="https://img.shields.io/badge/Telnet-Plaintext-red?style=for-the-badge" alt="Telnet">
  <img src="https://img.shields.io/badge/SSH-Encrypted-green?style=for-the-badge" alt="SSH">
</p>

</div>

---

## 📌 Overview

This lab uses **Wireshark** to capture and examine both **Telnet** and **SSH** sessions on the CyberOps Workstation.

The objective is to demonstrate the security difference between the two protocols:

- **Telnet** transmits the session data in plaintext.
- **SSH** encrypts the session data, making the captured traffic unreadable.

By following the TCP streams in Wireshark, the difference between plaintext and encrypted remote-access communications becomes visible.

---

## 🎯 Objectives

### Part 1 — Examine a Telnet Session with Wireshark
- Capture a Telnet session using Wireshark.
- Filter Telnet-related traffic.
- Follow the Telnet TCP stream.
- Observe usernames and passwords transmitted in plaintext.

### Part 2 — Examine an SSH Session with Wireshark
- Capture an SSH session using Wireshark.
- Filter SSH-related traffic.
- Follow the SSH TCP stream.
- Observe that the captured SSH session data is encrypted and unreadable.
- Compare SSH traffic with the Telnet session.

---

## 🧰 Required Resources

- **CyberOps Workstation virtual machine**
- Wireshark
- Telnet
- SSH
- Loopback interface (`lo`)

---

# 🧪 Part 1 — Examining a Telnet Session with Wireshark

## 1️⃣ Capture the Telnet Session

Start the CyberOps Workstation VM and log in using:

```text
Username: analyst
Password: cyberops
```

Open a terminal and start Wireshark:

```bash
wireshark &
```

Start a capture on the:

```text
Loopback: lo
```

---

## 2️⃣ Establish the Telnet Session

Open another terminal window and connect to localhost:

```bash
telnet localhost
```

Example session:

```text
Trying ::1...
Connected to localhost.
Escape character is '^]'.
Linux 4.10.10-1-ARCH (unallocated.barefruit.co.uk) (pts/12)

secOps login: analyst
Password:

Last login: Fri Apr 28 10:50:52 from localhost.localdomain
[analyst@secOps ~]$
```

Enter the credentials when prompted:

```text
Username: analyst
Password: cyberops
```

After providing the credentials, stop the Wireshark capture.

---

# 🔬 3️⃣ Examine the Telnet Traffic

In Wireshark, apply the following display filter:

```text
telnet
```

Click **Apply**.

This limits the displayed packets to Telnet-related traffic.

---

## 🌊 Follow the TCP Stream

Right-click one of the Telnet packets in the **Packet List** section.

Select:

```text
Follow → TCP Stream
```

The **Follow TCP Stream** window displays the captured Telnet session.

### 🔓 Important Observation

The Telnet session is displayed in **plaintext**, including the password.

Conceptually:

```text
Telnet Session
      │
      ▼
Wireshark Capture
      │
      ▼
Follow TCP Stream
      │
      ▼
Plaintext Session Data
      │
      ├── Username
      ├── Password
      └── Commands
```

> ⚠️ **Security Issue:** Sensitive credentials can be exposed when Telnet traffic is captured because the session data is transmitted without encryption.

### 👤 Duplicate Username Characters

The lab also notes that the username may appear with duplicate characters.

This occurs because of the **echo setting in Telnet**, which allows characters typed by the user to be displayed on the screen.

---

## 🚪 Exit the Telnet Session

After reviewing the TCP stream, close the Follow TCP Stream window.

Exit the Telnet session:

```bash
exit
```

---

# 🔐 Part 2 — Examining an SSH Session with Wireshark

In this part, an SSH session is established with localhost and captured using Wireshark.

---

## 1️⃣ Start an SSH Capture

Start another Wireshark capture using:

```text
Loopback: lo
```

---

## 2️⃣ Establish the SSH Session

From a terminal, connect to localhost:

```bash
ssh localhost
```

The first connection may display a host-authenticity prompt:

```text
The authenticity of host 'localhost (::1)' can't be established.
ECDSA key fingerprint is SHA256:1xZuV8NMeVsNQPRrzVf9nXHzdUP+EtgVouZVbWH80XA.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Enter:

```text
yes
```

Then enter the password when prompted:

```text
analyst@localhost's password:
```

After establishing the SSH session, stop the Wireshark capture.

---

# 🔎 3️⃣ Examine the SSH Traffic

Apply the following Wireshark display filter:

```text
ssh
```

Click **Apply**.

The capture will now display SSH-related traffic.

---

## 🌊 Follow the SSH TCP Stream

Right-click one of the **SSHv2** packets in the Packet List.

Select:

```text
Follow → TCP Stream
```

Examine the resulting Follow TCP Stream window.

### 🔒 Important Observation

Unlike the Telnet session, the SSH session data is **encrypted and unreadable** in the packet capture.

Conceptually:

```text
SSH Session
      │
      ▼
Wireshark Capture
      │
      ▼
Follow TCP Stream
      │
      ▼
Encrypted Session Data
      │
      └── Unreadable Without Decryption
```

---

# ⚖️ Telnet vs SSH

| Feature | Telnet | SSH |
|---|---|---|
| Remote access | ✅ | ✅ |
| Wireshark traffic can be captured | ✅ | ✅ |
| Session data visible in TCP stream | ✅ | ⚠️ Encrypted |
| Username visible in captured session | ✅ Plaintext | 🔒 Encrypted |
| Password visible in captured session | ✅ Plaintext | 🔒 Encrypted |
| Encrypted communication | ❌ | ✅ |
| Suitable for secure remote access | ❌ | ✅ |

> 💡 The lab demonstrates that encryption is a critical security difference between Telnet and SSH.

---

# 🔄 Traffic Analysis Workflow

```text
                    ┌──────────────────┐
                    │ CyberOps VM      │
                    └────────┬─────────┘
                             │
                   ┌─────────┴─────────┐
                   │                   │
                   ▼                   ▼
             ┌───────────┐       ┌───────────┐
             │  Telnet   │       │    SSH    │
             └─────┬─────┘       └─────┬─────┘
                   │                   │
                   ▼                   ▼
             ┌───────────┐       ┌───────────┐
             │ Wireshark │       │ Wireshark │
             │  Capture  │       │  Capture  │
             └─────┬─────┘       └─────┬─────┘
                   │                   │
                   ▼                   ▼
             ┌───────────┐       ┌───────────┐
             │ Follow    │       │ Follow    │
             │ TCP Stream│       │ TCP Stream│
             └─────┬─────┘       └─────┬─────┘
                   │                   │
                   ▼                   ▼
             ┌───────────┐       ┌───────────┐
             │ Plaintext │       │ Encrypted │
             │   Data    │       │   Data    │
             └───────────┘       └───────────┘
```

---

# 🛡️ Cybersecurity Relevance

This lab demonstrates an important network-security principle: **capturing traffic is not necessarily enough to understand protected communications**.

### Telnet

A captured Telnet session can expose sensitive information because the session data is transmitted in plaintext.

An analyst examining the TCP stream can observe information such as:

```text
Username
Password
Commands
Session Data
```

### SSH

SSH encrypts the communication between the client and remote system.

When the same type of capture is examined through Wireshark, the session data appears encrypted and unreadable.

This makes SSH preferable to Telnet for secure remote connections according to the lab's security comparison.

---

# 🧠 Key Takeaways

- 🔎 Wireshark can capture Telnet and SSH traffic.
- 📡 Both Telnet and SSH sessions can be observed at the packet level.
- 🔓 Telnet session data is visible in plaintext.
- 🔐 SSH session data is encrypted.
- 👤 Telnet can expose usernames and passwords in captured traffic.
- 🛡️ SSH protects remote-session communications through encryption.
- 🌐 Following a TCP stream is useful for examining application-layer traffic.
- 🚨 Unencrypted remote-access protocols can expose sensitive credentials during network interception.

---

# 💭 Reflection

### Why is SSH preferred over Telnet for remote connections?

The lab explains that both protocols can be used to access and execute commands on a remote system. However, **SSH provides secure communication by encrypting the communications**.

This prevents sensitive information, such as usernames and passwords, from being captured as readable plaintext during transmission.

---

<div align="center">

### 🔐 Telnet vs SSH

**Plaintext Remote Access vs Encrypted Remote Access**

</div>
