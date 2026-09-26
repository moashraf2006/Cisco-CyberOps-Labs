<div align="center">

# 🔐 Lab - Encrypting and Decrypting Data Using a Hacker Tool

### Cisco CyberOps Workstation | Password Recovery | Encrypted ZIP Files

<p>
  <img src="https://img.shields.io/badge/Cisco-CyberOps-blue?style=for-the-badge&logo=cisco" alt="Cisco CyberOps">
  <img src="https://img.shields.io/badge/Linux-Security-black?style=for-the-badge&logo=linux" alt="Linux">
  <img src="https://img.shields.io/badge/Tool-fcrackzip-red?style=for-the-badge" alt="fcrackzip">
  <img src="https://img.shields.io/badge/Topic-Password%20Recovery-orange?style=for-the-badge" alt="Password Recovery">
</p>

</div>

---

## 📌 Overview

This lab demonstrates how encrypted ZIP files can be created and how their passwords can be recovered using a Linux password-recovery utility.

The scenario focuses on a corporate policy requiring sensitive documents copied to removable media to be stored inside encrypted ZIP archives. A password is lost, and the cybersecurity analyst uses **`fcrackzip`** to attempt password recovery.

The lab also demonstrates how **password length affects the time required for brute-force password recovery**.

> ⚠️ **Important:** This lab is intended for instructional purposes only. The methods presented should **NOT** be used to secure truly sensitive data.

---

## 🎯 Objectives

### Part 1 — Create and Encrypt Files
- Create sample text files.
- Create encrypted ZIP archives using different password lengths.
- Attempt to open an encrypted ZIP file with an incorrect password.

### Part 2 — Recover Encrypted ZIP File Passwords
- Explore the `fcrackzip` utility.
- Recover passwords using brute-force methods.
- Compare password-recovery time as password length increases.
- Observe the effect of longer passwords on password-recovery difficulty.

---

## 🧰 Required Resources

- **CyberOps Workstation virtual machine**
- Linux terminal
- `zip`
- `unzip`
- `fcrackzip`

---

# 🧪 Part 1 — Create and Encrypt Files

## 1️⃣ Create the Working Directory

Start the **CyberOps Workstation VM** and open a terminal.

Verify that you are in the analyst home directory:

```bash
cd ~
```

Create a directory for the ZIP files:

```bash
mkdir Zip-Files
cd Zip-Files
```

---

## 2️⃣ Create Sample Text Files

Create three sample text files:

```bash
echo "This is a sample text file" > sample-1.txt
echo "This is a sample text file" > sample-2.txt
echo "This is a sample text file" > sample-3.txt
```

Verify the files:

```bash
ls -l
```

Expected files:

```text
sample-1.txt
sample-2.txt
sample-3.txt
```

---

## 🔐 3️⃣ Create Encrypted ZIP Files

The `zip -e` command creates an encrypted ZIP archive and prompts for a password.

### Create `file-1.zip`

```bash
zip -e file-1.zip sample*
```

Use a **1-character password**.

Example:

```text
Enter password:
Verify password:
```

---

### Create `file-2.zip`

```bash
zip -e file-2.zip sample*
```

Use a **2-character password**.

Example:

```text
R2
```

---

### Create `file-3.zip`

```bash
zip -e file-3.zip sample*
```

Use a **3-character password**.

Example:

```text
0B1
```

---

### Create `file-4.zip`

```bash
zip -e file-4.zip sample*
```

Use a **4-character password**.

Example:

```text
Y0Da
```

---

### Create `file-5.zip`

```bash
zip -e file-5.zip sample*
```

Use a **5-character password**.

Example:

```text
C-3P0
```

---

## 📂 4️⃣ Verify the Encrypted Archives

List the created ZIP files:

```bash
ls -l f*
```

Expected archives:

```text
file-1.zip
file-2.zip
file-3.zip
file-4.zip
file-5.zip
```

The example lab output shows each archive at approximately **643 bytes**.

---

## 🚫 5️⃣ Attempt to Open a ZIP with an Incorrect Password

Try to extract `file-1.zip`:

```bash
unzip file-1.zip
```

Enter an incorrect password.

The system will report:

```text
password incorrect--reenter:
```

After repeated incorrect attempts:

```text
skipping: sample-1.txt    incorrect password
skipping: sample-2.txt    incorrect password
skipping: sample-3.txt    incorrect password
```

This demonstrates that the ZIP contents cannot be extracted without the correct password.

---

# 🕵️ Part 2 — Recover Encrypted ZIP File Passwords

## 🔎 1️⃣ Introduction to `fcrackzip`

`fcrackzip` is a Linux utility that searches encrypted ZIP files and attempts to recover their passwords using brute-force methods.

Display the available options:

```bash
fcrackzip -h
```

For this lab, the main options are:

| Option | Purpose |
|---|---|
| `-v` | Verbose output |
| `-u` | Use unzip to verify the password |
| `-l` | Specify the possible password length |

The password-length option is particularly important because limiting the search space can significantly reduce the number of possibilities that must be tested.

---

# 🔓 2️⃣ Recover the Password of `file-1.zip`

The password is known to contain **1 character**, so run:

```bash
fcrackzip -vul 1-4 file-1.zip
```

Example result:

```text
found file 'sample-1.txt'
found file 'sample-2.txt'
found file 'sample-3.txt'

PASSWORD FOUND!!!!: pw == B
```

### ⏱️ Observed Time

The lab reports:

> It takes less than a second.

---

# 🔓 3️⃣ Recover the Password of `file-2.zip`

Run:

```bash
fcrackzip -vul 1-4 file-2.zip
```

Example result:

```text
PASSWORD FOUND!!!!: pw == R2
```

### ⏱️ Observed Time

The lab reports:

> It should take about a second.

---

# 🔓 4️⃣ Recover the Password of `file-3.zip`

Run:

```bash
fcrackzip -vul 1-4 file-3.zip
```

Example result:

```text
PASSWORD FOUND!!!!: pw == 0B1
```

### ⏱️ Observed Time

The lab states that results vary depending on the platform and password, but recovery should take approximately:

> **One or two seconds**

---

# 🔓 5️⃣ Recover the Password of `file-4.zip`

Run:

```bash
fcrackzip -vul 1-4 file-4.zip
```

Example result:

```text
checking pw X9M~
PASSWORD FOUND!!!!: pw == Y0Da
```

### ⏱️ Observed Time

The lab states that results vary depending on the platform and password, but recovery should take:

> **A few seconds**

---

# 🔓 6️⃣ Recover the Password of `file-5.zip`

Because the password contains five characters, extend the password-length range:

```bash
fcrackzip -vul 1-5 file-5.zip
```

Example result:

```text
checking pw C-H*~
PASSWORD FOUND!!!!: pw == C-3P0
```

### ⏱️ Observed Time

The lab states:

> About two minutes.

Actual time varies depending on the platform and the password used.

---

# 🧨 7️⃣ Test a Six-Character Password

The lab then demonstrates a **6-character password**.

Create another encrypted archive:

```bash
zip -e file-6.zip sample*
```

Use a six-character password.

Example:

```text
JarJar
```

Now attempt password recovery:

```bash
fcrackzip -vul 1-6 file-6.zip
```

### ⏱️ Expected Result

The lab states that recovery can take:

> **Much longer — potentially hours.**

The exact time depends on the platform and the password.

---

# 📊 Password Length vs. Recovery Time

The lab demonstrates the relationship between password length and brute-force recovery time:

| ZIP File | Password Length | Example Password | Approx. Recovery Time |
|---|---:|---|---|
| `file-1.zip` | 1 | `B` | Less than a second |
| `file-2.zip` | 2 | `R2` | About a second |
| `file-3.zip` | 3 | `0B1` | About 1–2 seconds |
| `file-4.zip` | 4 | `Y0Da` | A few seconds |
| `file-5.zip` | 5 | `C-3P0` | About 2 minutes |
| `file-6.zip` | 6 | `JarJar` | Much longer / potentially hours |

> 💡 **Note:** These times are the example observations provided by the lab. Actual results vary according to the system, password, and search space.

---

# 🧠 Key Security Concept

The lab illustrates why **password length has a major impact on brute-force password recovery**.

As the number of possible passwords increases, a brute-force tool has to test more combinations before finding the correct password.

Conceptually:

```text
Short Password
      │
      ▼
Smaller Search Space
      │
      ▼
Fewer Combinations
      │
      ▼
Faster Recovery
```

```text
Longer Password
      │
      ▼
Larger Search Space
      │
      ▼
More Combinations
      │
      ▼
Longer Recovery Time
```

The lab concludes that longer passwords are more secure because they take longer to discover.

---

# 🔄 Attack / Recovery Workflow

```text
┌─────────────────────────┐
│ Create Sample Files     │
│ sample-1.txt ...        │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│ Create Encrypted ZIP    │
│ zip -e file-X.zip       │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│ Password Unknown/Lost   │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│ Run fcrackzip           │
│ Brute-force recovery    │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│ Password Recovered      │
└─────────────────────────┘
```

---

# 🛡️ Cybersecurity Relevance

This lab demonstrates several practical cybersecurity concepts:

- 🔐 Encrypted archive protection
- 🔑 Password security
- 🧮 Brute-force password attacks
- 🕵️ Password recovery
- 💾 Removable-media security
- ⏱️ Password length and attack time
- 🛡️ Defensive awareness against credential attacks

Password-recovery utilities such as `fcrackzip` can have legitimate uses, such as recovering access to authorized files when credentials have been lost.

However, the same techniques can be abused by attackers attempting to recover passwords from stolen or compromised encrypted archives.

---

# ⚠️ Security Considerations

The lab emphasizes that:

> **Longer and more complex passwords are more secure because they increase the time required for password discovery.**

It also demonstrates that a short password should not be considered sufficient protection against password-recovery tools.

The lab asks:

**How long would you recommend a password needs to be for it to be secure?**

The source material leaves this as an exercise and does not provide a specific recommended length.

---

# 📝 Lab Takeaways

- `zip -e` can be used to create password-protected ZIP archives.
- `unzip` requires the correct password to extract encrypted contents.
- `fcrackzip` can attempt to recover encrypted ZIP passwords.
- The `-l` option controls the password-length range searched.
- Password length directly affects the brute-force search space.
- Longer passwords generally require more time to recover.
- Actual cracking time depends on the system and password characteristics.
- Password recovery tools can be useful for authorized security and recovery tasks but can also be abused by attackers.

---

<div align="center">

### 🔐 Cybersecurity Lab Complete

**Cisco CyberOps | Password Recovery | Brute Force | Encrypted ZIP Files**

</div>
