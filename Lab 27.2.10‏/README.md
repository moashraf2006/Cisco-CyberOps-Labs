<div align="center">

# 🧪 Lab - Extract an Executable from a PCAP

**Cisco CyberOps — Network Traffic & Malware Analysis**

[![Cisco CyberOps](https://img.shields.io/badge/Cisco-CyberOps-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)](https://www.netacad.com/)
[![Wireshark](https://img.shields.io/badge/Wireshark-PCAP%20Analysis-1679A7?style=for-the-badge&logo=wireshark&logoColor=white)](https://www.wireshark.org/)
[![Linux](https://img.shields.io/badge/Linux-CLI-FCC624?style=for-the-badge&logo=linux&logoColor=black)]
[![Malware Analysis](https://img.shields.io/badge/Focus-Malware%20Analysis-critical?style=for-the-badge)]

</div>

---

## 🎯 Objectives

- **Part 1:** Analyze pre-captured logs and traffic captures.
- **Part 2:** Extract downloaded files from a PCAP.

---

## 🧠 Background / Scenario

Looking at logs is very important, but it is also important to understand how network transactions happen at the packet level.

In this lab, a previously captured PCAP file is analyzed to identify the traffic associated with a malware download and extract the downloaded executable.

The capture file used in the lab is:

```text
nimda.download.pcap
```

The lab uses the stored copy located in:

```text
/home/analyst/lab.support.files/pcaps
```

> ⚠️ **Security Note:** The extracted file is an executable associated with a malware-analysis exercise. Treat it as potentially unsafe and only handle it inside an appropriate controlled analysis environment.

---

## 🛠️ Required Resources

- CyberOps Workstation virtual machine
- Wireshark
- Terminal / Linux CLI
- `nimda.download.pcap`

---

# 🔎 Part 1 — Analyze Pre-Captured Logs and Traffic Captures

## 1. Navigate to the PCAP Directory

Change to the directory containing the stored packet captures:

```bash
cd lab.support.files/pcaps
```

List the available files:

```bash
ls -l
```

Example output:

```text
total 7460
-rw-r--r-- 1 analyst analyst 3510551 Aug  7 15:25 lab_prep.pcap
-rw-r--r-- 1 analyst analyst  371462 Jun 22 10:47 nimda.download.pcap
-rw-r--r-- 1 analyst analyst 3750153 May 25 11:10 wannacry_download_pcap.pcap
```

---

## 2. Open the PCAP in Wireshark

Launch Wireshark with the capture:

```bash
wireshark nimda.download.pcap &
```

The PCAP contains the packets associated with a previous malware-download activity.

> 💡 **PCAP Compatibility:** `tcpdump` and Wireshark use the same packet-capture file format, so PCAP files created by one tool can be opened and analyzed by the other.

---

## 3. Analyze the HTTP Request

Select the **fourth packet** in the capture and expand the **Hypertext Transfer Protocol** section.

The first three packets represent the TCP three-way handshake:

```text
Client                    Server
  |                         |
  | -------- SYN ---------> |
  | <------ SYN/ACK ------- |
  | -------- ACK ---------> |
  |                         |
```

The fourth packet contains the request for the downloaded file.

The request was made using:

```text
HTTP GET
```

This confirms that the malware file was requested over HTTP.

---

## 4. Follow the TCP Stream

Because HTTP runs over TCP, Wireshark can reconstruct the TCP transaction.

Select the first TCP packet in the capture — the **SYN packet**.

Right-click it and select:

```text
Follow → TCP Stream
```

Wireshark displays the contents of the complete selected TCP flow.

### ❓ Question: What are the symbols shown in the Follow TCP Stream window?

The symbols are the actual contents of the downloaded file.

Because the downloaded file is binary data, Wireshark does not know how to represent it as normal readable text. The displayed symbols are Wireshark's best attempt to interpret the binary data as text.

---

### ❓ Question: Why are there readable words among the symbols?

The readable fragments are strings contained within the executable code.

These strings can include messages that the program provides to the user while it runs. Although interpreting strings is more of an art than a science, a skilled analyst can sometimes extract useful information from these fragments.

---

## 🧩 Challenge Question — Identify the Executable

The downloaded file is named:

```text
W32.Nimda.Amm.exe
```

However, the lab explains that this is **not the famous Nimda worm**. The executable was renamed for the exercise.

By scrolling through the contents displayed in the **Follow TCP Stream** window, readable fragments reveal that the executable is actually:

```text
cmd.exe
```

### 🔍 Finding

The executable is identified as the Microsoft Windows **Command Prompt executable (`cmd.exe`)** based on the word fragments visible in the reconstructed TCP stream.

---

# 📥 Part 2 — Extract Downloaded Files from PCAP

A packet capture contains the packets associated with network traffic. When a PCAP includes a file download, Wireshark can reconstruct and export the downloaded object.

In this lab, the downloaded executable can be extracted directly from the HTTP traffic.

---

## 1. Identify the HTTP GET Request

Return to the fourth packet in:

```text
nimda.download.pcap
```

The HTTP GET request was generated from:

```text
209.165.200.235
```

to:

```text
209.165.202.133
```

The **Info** column identifies the packet as the GET request for the file.

---

## 2. Export the HTTP Object

With the HTTP GET request packet selected, navigate to:

```text
File → Export Objects → HTTP
```

Wireshark displays the HTTP objects found in the TCP flow containing the GET request.

In this capture, the HTTP object is:

```text
W32.Nimda.Amm.exe
```

---

### ❓ Question: Why is W32.Nimda.Amm.exe the only file in the capture?

The capture was started immediately before the download and stopped immediately after it.

Therefore, no other traffic was captured while the packet capture was active.

---

## 3. Save the Extracted Executable

In the HTTP object list:

1. Select `W32.Nimda.Amm.exe`.
2. Click **Save As**.
3. Navigate to the `analyst` home directory.
4. Save the file there.

The resulting file should be located at:

```text
/home/analyst/W32.Nimda.Amm.exe
```

---

## 4. Verify the Extracted File

Change to the analyst home directory:

```bash
cd /home/analyst
```

List the files:

```bash
ls -l
```

Example output includes:

```text
-rw-r--r-- 1 analyst analyst 345088 Jun 22 15:12 W32.Nimda.Amm.exe
```

### ❓ Question: Was the file saved?

**Yes.**

The file appears in the `/home/analyst` directory.

---

## 5. Identify the File Type

Use the `file` command:

```bash
file W32.Nimda.Amm.exe
```

Example output:

```text
W32.Nimda.Amm.exe: PE32+ executable (console) x86-64, for MS Windows
```

This confirms that the extracted file is a Windows executable.

---

# 🦠 Malware Analysis — Probable Next Step

### ❓ Question: What would be a probable next step for a security analyst?

The goal of malware analysis is to identify the type of malware and analyze its behavior.

The extracted malware should therefore be moved to a **controlled environment** and executed for observation. Malware-analysis environments commonly rely on virtual machines and sandboxing to prevent damage to non-test systems.

Analysts can monitor aspects such as:

- Resource usage
- Network connections
- Operating system changes
- Malware execution behavior

The lab also identifies Internet-based malware analysis tools as another option, with **VirusTotal** given as an example.

> ⚠️ **Important:** Do not execute an unknown executable on a normal workstation or production system. Malware analysis should be performed in an isolated and controlled environment.

---

# 🔄 Lab Workflow

```text
┌──────────────────────────┐
│   nimda.download.pcap    │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│       Wireshark          │
│    Analyze PCAP Traffic  │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ Identify HTTP GET        │
│ Request / TCP Flow       │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│    Follow TCP Stream     │
│ Reconstruct Transaction  │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│  Export Objects → HTTP   │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ W32.Nimda.Amm.exe        │
│      Extracted           │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│       file command       │
│ Identify PE32+ x86-64    │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ Controlled Malware       │
│ Analysis Environment     │
└──────────────────────────┘
```

---

# 🔐 Cybersecurity Relevance

This lab demonstrates an important workflow used in network and malware analysis:

| Technique | Security Use |
|---|---|
| **PCAP Analysis** | Investigate network activity after an incident |
| **HTTP Analysis** | Identify files and requests transferred over HTTP |
| **TCP Stream Reconstruction** | Reassemble application data from TCP packets |
| **HTTP Object Export** | Recover transferred files from packet captures |
| **File Identification** | Determine whether an extracted object is an executable |
| **String Analysis** | Extract potentially useful information from binaries |
| **Controlled Execution** | Observe suspicious software without exposing production systems |

### 🧠 Key Takeaways

- PCAP files can preserve evidence of file downloads.
- Wireshark can reconstruct TCP conversations.
- **Follow TCP Stream** can expose the contents of a TCP transaction.
- Binary executables may appear as unreadable symbols when interpreted as text.
- Readable strings embedded in executables can provide useful clues.
- Wireshark can extract HTTP objects directly from captured traffic.
- The `file` command can identify the general type of an extracted executable.
- Suspicious binaries should be analyzed in controlled and isolated environments.

---

# 📝 Key Commands

```bash
# Navigate to the PCAP directory
cd lab.support.files/pcaps

# List packet captures
ls -l

# Open the capture in Wireshark
wireshark nimda.download.pcap &

# Navigate to the analyst home directory
cd /home/analyst

# Verify the extracted executable
ls -l

# Identify the executable type
file W32.Nimda.Amm.exe
```

---

## 🧪 Skills Practiced

```text
PCAP Analysis
    ↓
Wireshark
    ↓
HTTP Traffic Analysis
    ↓
TCP Stream Reconstruction
    ↓
HTTP Object Extraction
    ↓
Executable Identification
    ↓
Malware Analysis Preparation
```

---

<div align="center">

### 🛡️ Cisco CyberOps — Network Traffic & Malware Analysis

**Analyze the traffic. Recover the evidence. Understand the behavior.**

</div>
