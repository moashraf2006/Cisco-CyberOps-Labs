<div align="center">

# 🧪 Lab - Interpret HTTP and DNS Data to Isolate Threat Actor

**Cisco CyberOps — Threat Investigation & Network Data Exfiltration**

[![Cisco CyberOps](https://img.shields.io/badge/Cisco-CyberOps-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)](https://www.netacad.com/)
[![Security Onion](https://img.shields.io/badge/Security%20Onion-SOC%20Analysis-8B0000?style=for-the-badge)]
[![Kibana](https://img.shields.io/badge/Kibana-Log%20Analysis-005571?style=for-the-badge&logo=kibana&logoColor=white)]
[![SQL Injection](https://img.shields.io/badge/Focus-SQL%20Injection-critical?style=for-the-badge)]
[![DNS Exfiltration](https://img.shields.io/badge/Focus-DNS%20Exfiltration-orange?style=for-the-badge)]

</div>

---

## 🎯 Objectives

- **Part 1:** Investigate an SQL Injection attack.
- **Part 2:** Investigate DNS data exfiltration.
- Use Kibana to investigate documented HTTP and DNS exploitation activity.
- Identify the source and destination of the suspicious HTTP traffic.
- Determine what sensitive information was accessed through the HTTP attack.
- Identify encoded information hidden inside DNS queries.
- Decode the exfiltrated data and determine its significance.

---

## 🧠 Background / Scenario

MySQL is a popular database used by numerous web applications. Unfortunately, SQL injection is a common web-hacking technique.

SQL injection is a code-injection technique in which an attacker executes malicious SQL statements to control a web application's database server.

DNS servers translate domain names into IP addresses. This service can also be used to exfiltrate data.

Cybersecurity personnel have determined that an exploit occurred and that data containing personally identifiable information (**PII**) may have been exposed to threat actors.

In this lab, **Kibana** is used to investigate HTTP and DNS activity and determine what information was accessed and exfiltrated.

> ⚠️ **Security Focus:** This lab demonstrates how legitimate network protocols such as HTTP and DNS can be abused to access sensitive information and move data out of a network.

---

## 🛠️ Required Resources

- Security Onion virtual machine
- Kibana
- Security Onion log data
- Terminal / Linux CLI
- `xxd`

---

# 🔎 Part 1 — Investigate an SQL Injection Attack

The first investigation focuses on unauthorized access to sensitive information stored on a web server.

The goal is to use Kibana to identify:

- The source of the attack
- The destination web server
- The HTTP activity
- The information requested by the attacker
- Evidence of SQL injection
- Information returned to the attacker

---

## Step 1 — Change the Timeframe

The exploit occurred during **June 2020**.

Kibana normally displays data from the last 24 hours, so the timeframe must be changed.

### 1. Start Security Onion

Log in to the Security Onion VM using:

```text
Username: analyst
Password: cyberops
```

### 2. Check Security Onion Services

Run:

```bash
sudo so-status
```

All services should report:

```text
[ OK ]
```

Example:

```text
Status: securityonion
* sguil server                                                       [  OK  ]

Status: seconion-import
* pcap_agent (sguil)                                                 [  OK  ]
* snort_agent-1 (sguil)                                              [  OK  ]
* barnyard2-1 (spooler, unified2 format)                             [  OK  ]

Status: Elastic stack
* so-elasticsearch                                                   [  OK  ]
* so-logstash                                                        [  OK  ]
* so-kibana                                                          [  OK  ]
* so-freqserver                                                      [  OK  ]
```

> ⏳ The status check may take a few minutes.

### 3. Open Kibana

Use the Kibana shortcut on the Security Onion desktop.

Log in with:

```text
Username: analyst
Password: cyberops
```

Kibana provides pre-built dashboards and visualizations for monitoring and analysis.

### 4. Set the Time Range

In the upper-right corner:

```text
Last 24 hours
```

Change the range to an **Absolute** time range covering the entire month of:

```text
June 2020
```

Then click:

```text
Go
```

The dashboard should now display the relevant logs from June 2020.

---

# 🌐 Step 2 — Filter for HTTP Traffic

Because the threat actor accessed data stored on a web server, investigate HTTP traffic.

In Kibana, select:

```text
HTTP
```

under the:

```text
Zeek Hunting
```

section.

> 💡 **Terminology Note:** Kibana may still refer to Zeek using its previous name, **Bro**.

---

## 📊 HTTP Investigation Findings

### Source IP Address

```text
209.165.200.227
```

### Destination IP Address

```text
209.165.200.235
```

### Destination Port

```text
80
```

This indicates HTTP traffic directed toward port `80`.

---

## 🕒 First HTTP Log Entry

The first HTTP result has the following timestamp:

```text
June 12, 2020, 21:30:09.445
```

### Event Type

```text
bro_http
```

Kibana still uses the old **Bro** naming convention for this event type.

---

## 🔍 Inspect the HTTP Log

Scroll to the HTTP Logs and expand the first result by clicking the arrow beside the timestamp.

The message field contains details about the HTTP GET request.

The `uri` information contains references to:

```text
username
ccid
ccnumber
ccv
expiration
password
```

### Significance

The requested information appears to be related to **credit card information**.

This is significant because the HTTP request is targeting sensitive information that may contain PII and financial data.

---

# 💉 Step 3 — Investigate the SQL Injection

Some fields in the Kibana log entry link to other analysis tools.

Click the value in the:

```text
alert _id
```

field.

This opens information from **capME!**, a web interface that allows the analyst to view a PCAP transcript.

### capME! Transcript

The transcript uses different colors:

```text
Blue → HTTP requests from SRC
Red  → Responses from DST
```

At the beginning of the transcript, the following portion is significant:

```text
username='+union+select+ccid,ccnumber,ccv,expiration,null+from+credit_cards+--+&password=
```

The presence of SQL keywords such as:

```text
UNION
SELECT
```

indicates an attempted SQL injection attack.

The injected SQL statement attempts to retrieve:

```text
ccid
ccnumber
ccv
expiration
```

from:

```text
credit_cards
```

### 🔬 Attack Flow

```text
Attacker
   │
   │ HTTP GET
   │
   ▼
Web Application
   │
   │ Injected SQL
   │
   ▼
MySQL Database
   │
   │ Query sensitive data
   │
   ▼
HTTP Response
   │
   │ Returned information
   ▼
Attacker
```

---

# 👤 Investigate Returned User Information

Use:

```text
Ctrl + F
```

to search for:

```text
username
```

The term appears in normal web-interface content, but later in the transcript something unusual appears.

The response contains what appears to be a list of:

- Usernames
- Passwords
- Other sensitive information

This indicates that information from the database was returned to the attacker.

### Examples from the Lab

| Username / Value | Password / Value | Signature / Related Data |
|---|---|---|
| `4444111122223333` | `745` | — |
| `7746536337776330` | `722` | `2012-03-01` |
| `8242325748474749` | `461` | `2015-04-01` |
| `7725653200487633` | `230` | `2016-03-01` |
| `1234567812345678627` | `627` | `2017-06-01` |
| — | — | `2018-11-01` |

> ⚠️ These values are reproduced from the provided Cisco lab as investigation artifacts.

---

# 🚨 SQL Injection Findings

| Indicator | Finding |
|---|---|
| Source IP | `209.165.200.227` |
| Destination IP | `209.165.200.235` |
| Destination Port | `80` |
| Protocol | HTTP |
| Event Type | `bro_http` |
| Attack Type | SQL Injection |
| SQL Keywords | `UNION`, `SELECT` |
| Target Table | `credit_cards` |
| Requested Data | `ccid`, `ccnumber`, `ccv`, `expiration` |
| Exposed Information | Sensitive database records |

---

# 🧬 Part 2 — Analyze DNS Exfiltration

A network administrator has noticed unusually long DNS queries containing strange-looking subdomains.

The goal is to investigate the anomaly and determine whether the DNS requests contain hidden data.

---

# 🌐 Step 1 — Filter for DNS Traffic

Return to the top of the Kibana Dashboard.

Clear the existing filters and search terms.

Click:

```text
Home
```

under the Navigation section.

The timeframe should still include:

```text
June 2020
```

Under **Zeek Hunting**, select:

```text
DNS
```

Kibana displays DNS-related metrics and visualizations, including:

- DNS Log Count
- Destination Port
- DNS query types
- DNS response codes
- DNS clients
- DNS servers
- DNS phishing attempts
- Top DNS queries by domain

---

# 🔍 Step 2 — Review DNS Entries

Review the DNS query types.

Examples include:

```text
A
AAAA
NB
PTR
```

You can also inspect DNS response codes and identify the most active DNS clients and servers.

Further down the dashboard, examine the top DNS queries by domain name.

A suspicious pattern appears:

```text
<very-long-subdomain>.ns.example.com
```

The unusually long subdomains attached to:

```text
ns.example.com
```

should be investigated.

---

## 🎯 Filter for example.com

Return to the search bar and enter:

```text
example.com
```

Click:

```text
Update
```

The log count becomes smaller because the results are now limited to requests associated with `example.com`.

---

## 🖥️ DNS Client and Server

The investigation identifies:

| Role | IP Address |
|---|---|
| DNS Client | `192.168.0.11` |
| DNS Server | `209.165.200.235` |

---

# 🧩 Step 3 — Determine the Exfiltrated Data

Further down the DNS results, four unique log entries contain unusually long subdomains attached to:

```text
ns.example.com
```

The long strings consist of characters from:

```text
0-9
a-f
```

This pattern resembles **hexadecimal-encoded data** rather than normal DNS subdomain names.

---

## 📥 Export the DNS Queries

Click:

```text
Export: Raw
```

The DNS queries are downloaded as a CSV file to:

```text
/home/analyst/Downloads
```

Open the exported file in a text editor.

Remove the surrounding text from the subdomains, leaving only the hexadecimal characters.

The resulting data should resemble:

```text
434f4e464944454e5449414c20444f43554d454e540a444f204e4f542053
484152450a5468697320646f63756d656e7420636f6e7461696e7320696e
666f726d6174696f6e2061626f757420746865206c617374207365637572
697479206272656163682e0a
```

Save the edited file with the original filename.

---

# 🔓 Decode the Hexadecimal Data

Use `xxd` to reverse the hexadecimal encoding:

```bash
xxd -r -p "DNS - Queries.csv" > secret.txt
```

Then display the decoded content:

```bash
cat secret.txt
```

The decoded content is:

```text
CONFIDENTIAL DOCUMENT
DO NOT SHARE
This document contains information about the last security breach.
```

---

# 🕵️ DNS Exfiltration Findings

### Were the subdomains actually subdomains?

No.

The apparently long subdomains were actually carrying encoded data.

The decoded message was:

```text
CONFIDENTIAL DOCUMENT
DO NOT SHARE
This document contains information about the last security breach.
```

---

## 📡 What Does This Imply?

The results indicate that the DNS requests were separate, coordinated requests containing hidden content.

The larger significance is that DNS queries can be abused to hide and transmit information outside a network.

### DNS Exfiltration Concept

```text
Sensitive Data
      │
      ▼
Encode Data
      │
      ▼
Split Into Chunks
      │
      ▼
Create DNS Queries
      │
      ▼
Long Encoded Subdomains
      │
      ▼
DNS Server
      │
      ▼
Reassemble + Decode
      │
      ▼
Original Data
```

---

# 🦠 What May Have Created the Encoded DNS Queries?

The lab suggests that malware may have generated these requests by:

1. Cycling through documents on the host.
2. Encoding their contents into hexadecimal.
3. Using the hexadecimal strings as DNS subdomains.
4. Sending the resulting DNS queries externally.

Example conceptual pattern:

```text
Original Document
       ↓
Hexadecimal Encoding
       ↓
Long DNS Subdomain
       ↓
DNS Query
       ↓
External DNS Infrastructure
```

DNS may be selected because DNS requests are commonly sent from internal networks to the Internet and may not always receive the same level of monitoring as other protocols.

---

# 🔐 Investigation Summary

| Investigation | Evidence | Finding |
|---|---|---|
| HTTP | `209.165.200.227 → 209.165.200.235:80` | Suspicious HTTP activity |
| HTTP URI | `username`, `ccnumber`, `ccv`, `expiration` | Sensitive information targeted |
| SQL Keywords | `UNION`, `SELECT` | SQL injection evidence |
| Database Target | `credit_cards` | Sensitive database accessed |
| HTTP Response | User/account information | Data returned to attacker |
| DNS Client | `192.168.0.11` | Source of suspicious DNS queries |
| DNS Server | `209.165.200.235` | DNS destination |
| DNS Pattern | Long hexadecimal subdomains | Potential encoded data |
| Decoded Data | Confidential document | DNS data exfiltration |

---

# 🧠 SOC Investigation Workflow

```text
                 Security Onion
                       │
                       ▼
                    Kibana
                       │
          ┌────────────┴────────────┐
          ▼                         ▼
     HTTP Hunting              DNS Hunting
          │                         │
          ▼                         ▼
    Identify Source          Identify Long Queries
          │                         │
          ▼                         ▼
    Inspect HTTP URI        Filter example.com
          │                         │
          ▼                         ▼
    capME! Transcript        Export Raw Queries
          │                         │
          ▼                         ▼
   Identify SQL Injection    Extract Hexadecimal
          │                         │
          ▼                         ▼
    Sensitive Data Access       xxd Decode
          │                         │
          └────────────┬────────────┘
                       ▼
                Determine Scope
                & Exfiltration
```

---

# 🛡️ Cybersecurity Relevance

This lab demonstrates how a SOC analyst can correlate network logs and packet-level evidence to investigate attacks and potential data exfiltration.

| Technique | Security Application |
|---|---|
| **Kibana Hunting** | Search and investigate large volumes of security logs |
| **HTTP Analysis** | Identify suspicious web requests |
| **SQL Injection Detection** | Recognize malicious SQL keywords and query patterns |
| **PCAP Transcript Analysis** | Inspect reconstructed HTTP transactions |
| **DNS Hunting** | Identify anomalous DNS behavior |
| **Encoded Data Detection** | Recognize suspicious hexadecimal data |
| **Data Decoding** | Recover hidden information from network artifacts |
| **Threat Investigation** | Connect indicators across multiple protocols |

---

# 🎯 Key Takeaways

- SQL injection can allow attackers to manipulate database queries through vulnerable web applications.
- HTTP logs can reveal suspicious URI parameters and requested sensitive information.
- `UNION` and `SELECT` in unexpected HTTP input can be indicators of SQL injection.
- capME! can provide a transcript view of HTTP requests and responses.
- DNS can be abused as a covert channel for data exfiltration.
- Unusually long and structured DNS subdomains deserve investigation.
- Hexadecimal-looking DNS labels may contain encoded data.
- Exporting and decoding DNS query data can reveal the information being exfiltrated.
- Effective SOC investigations combine log analysis, network evidence, and data decoding.

---

## 🧪 Skills Practiced

```text
Security Onion
      ↓
Kibana
      ↓
Log Hunting
      ↓
HTTP Investigation
      ↓
SQL Injection Identification
      ↓
PCAP Transcript Analysis
      ↓
DNS Investigation
      ↓
Anomalous Query Detection
      ↓
Hexadecimal Data Extraction
      ↓
Data Decoding
      ↓
Threat & Exfiltration Analysis
```

---

<div align="center">

### 🛡️ Cisco CyberOps — Threat Investigation

**Hunt the evidence. Decode the traffic. Understand the attack.**

</div>
