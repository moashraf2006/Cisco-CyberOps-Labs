<div align="center">

# 🔐 Lab - Hashing Things Out

**Cisco Networking Academy | OpenSSL | SHA-2 | File Integrity**

<p>
  <img src="https://img.shields.io/badge/Topic-Hashing-6f42c1?style=for-the-badge" alt="Hashing">
  <img src="https://img.shields.io/badge/OpenSSL-Hashing-721412?style=for-the-badge" alt="OpenSSL">
  <img src="https://img.shields.io/badge/SHA--2-SHA256%20%7C%20SHA512-blue?style=for-the-badge" alt="SHA-2">
  <img src="https://img.shields.io/badge/Cisco-NetAcad-1BA0D7?style=for-the-badge" alt="Cisco NetAcad">
</p>

<p>
  Generate cryptographic hashes, observe the effect of file modifications, compare hashing tools, and verify file integrity.
</p>

</div>

---

## 🎯 Objectives

| Part | Objective |
|---|---|
| **Part 1** | Hashing a Text File with OpenSSL |
| **Part 2** | Verifying Hashes |

---

## 🧰 Required Resources

- 💻 CyberOps Workstation virtual machine
- 🖥️ Terminal
- 🔐 OpenSSL
- 📝 `nano`
- 📦 Sample files from the lab

---

# 🧠 Background

Hash functions are mathematical algorithms that take data as input and generate a fixed-size, unique string of characters called a **hash**.

A key property demonstrated in this lab is that even a very small change to the input data produces a completely different hash.

Hashes can therefore be calculated before and after data transmission and compared to determine whether the data has changed.

---

# 🔐 Part 1 — Hashing a Text File with OpenSSL

## 1. Navigate to the Lab Files

Open a terminal and change to:

```bash
cd /home/analyst/lab.support.files/
```

List the contents of the text file:

```bash
cat letter_to_grandma.txt
```

The original file contains:

```text
Hi Grandma,
I am writing this letter to thank you for the chocolate chip cookies you sent me.
I got them this morning and I have already eaten half of the box! They are absolutely
delicious!
I wish you all the best. Love,
Your cookie-eater grandchild.
```

---

## 2. Generate a SHA-256 Hash

Use OpenSSL to generate a SHA-2-256 hash:

```bash
openssl sha256 letter_to_grandma.txt
```

### Original Hash

```text
deff9c9bbece44866796ff6cf21f2612fbb77aa1b2515a900bafb29be118080b
```

The OpenSSL output identifies:

- The hashing algorithm: **SHA-256**
- The input file: `letter_to_grandma.txt`
- The resulting hash after the `=` sign

---

## ✏️ 3. Modify the File

Open the file with `nano`:

```bash
nano letter_to_grandma.txt
```

Change:

```text
Hi Grandma
```

to:

```text
Hi Grandpa
```

Only one character is changed:

```text
m → p
```

Save and exit `nano` using:

```text
CTRL + X
Y
Enter
```

---

## 🔄 4. Generate the SHA-256 Hash Again

Run:

```bash
openssl sha256 letter_to_grandma.txt
```

### Modified Hash

```text
43302c4500b7c4b8e574ba27a59d83267812493c029fd054c9242f3ac73100bc
```

### Observation

The new hash is **completely different** from the original hash even though only one character was changed in the file.

This demonstrates the effect described in the lab: a small modification to the input produces a substantially different hash.

---

# 🔢 SHA-512 Hashing

A longer hash can also be generated using SHA-2-512:

```bash
openssl sha512 letter_to_grandma.txt
```

### SHA-512 Result

```text
7c35db79a06aa30ae0f6de33f2322fd419560ee9af9cedeb6e251f2f1c4e99e0bbe5d2fc32ce5
01468891150e3be7e288e3e568450812980c9f8288e3103a1d3
```

---

# 🧪 Comparing Hashing Tools

The same file can be hashed using `sha256sum` and `sha512sum`.

## SHA-256

```bash
sha256sum letter_to_grandma.txt
```

Result:

```text
43302c4500b7c4b8e574ba27a59d83267812493c029fd054c9242f3ac73100bc
```

## SHA-512

```bash
sha512sum letter_to_grandma.txt
```

Result:

```text
7c35db79a06aa30ae0f6de33f2322fd419560ee9af9cedeb6e251f2f1c4e99e0bbe5d2fc32ce5
01468891150e3be7e288e3e568450812980c9f8288e3103a1d3
```

### Comparison

| Algorithm | Tool | Result |
|---|---|---|
| SHA-256 | `openssl sha256` | `43302c4500b7c4b8e574ba27a59d83267812493c029fd054c9242f3ac73100bc` |
| SHA-256 | `sha256sum` | Same hash |
| SHA-512 | `openssl sha512` | Same SHA-512 hash |
| SHA-512 | `sha512sum` | Same hash |

**Conclusion:** The hashes match because both tools use the same hashing algorithm and the same input data.

---

# 🧬 SHA-2 and SHA-3

The lab identifies **SHA-2** as the recommended standard for hashing and notes that SHA-3 is the newest hashing algorithm and is expected to eventually replace the SHA-2 family.

The CyberOps Workstation VM used in the lab supports:

```text
SHA-2-224
SHA-2-256
SHA-2-512
```

Corresponding commands include:

```bash
sha224sum
sha256sum
sha512sum
```

---

# 🛡️ Part 2 — Verifying Hashes

Hashes can be used to verify the integrity of downloaded files.

The lab uses:

```text
sample.img
sample.img_SHA256.sig
```

The `.sig` file contains the SHA-2-256 hash calculated by the website.

---

## 1. View the Published Hash

Run:

```bash
cat sample.img_SHA256.sig
```

Expected hash:

```text
c56c4724c26eb0157963c0d62b76422116be31804a39c82fd44ddf0ca5013e6a
```

---

## 2. Calculate the Downloaded File's Hash

Run:

```bash
sha256sum sample.img
```

Result:

```text
c56c4724c26eb0157963c0d62b76422116be31804a39c82fd44ddf0ca5013e6a  sample.img
```

---

## 🔎 Hash Comparison

| Source | SHA-256 Hash |
|---|---|
| Published hash | `c56c4724c26eb0157963c0d62b76422116be31804a39c82fd44ddf0ca5013e6a` |
| Calculated hash | `c56c4724c26eb0157963c0d62b76422116be31804a39c82fd44ddf0ca5013e6a` |

### ✅ Result

The hashes match.

Based on the lab's verification method, the `sample.img` file was downloaded without errors because the hash calculated after the download matches the hash provided with the file.

> ⚠️ **Important:** The lab notes that hash comparison is useful for detecting transmission errors, but stronger methods such as **GPG** can provide better assurance that a downloaded file has not been modified by a third party and is actually the file intended by the publisher.

---

# 📊 Hashing Workflow

```text
Original File
     │
     ▼
Generate Hash
     │
     ▼
Modify File
     │
     ▼
Generate Hash Again
     │
     ▼
Compare Hashes
     │
     ├── Different → File changed
     │
     └── Same → Input data unchanged
```

For downloaded files:

```text
Published Hash
      │
      │ Compare
      ▼
Calculated Hash
      │
      ├── Match → Integrity verified
      │
      └── Different → Possible modification/error
```

---

# 🧠 Key Takeaways

- 🔐 Hash functions generate fixed-size values from input data.
- ⚡ A small change to the input can produce a completely different hash.
- 🔎 SHA-256 and SHA-512 can be generated using OpenSSL.
- 🧰 `sha256sum` and `sha512sum` can independently verify OpenSSL-generated hashes.
- 📦 Hash comparison can be used to verify file integrity.
- 🛡️ Matching hashes indicate that the compared data produced the same hash value.
- 🔑 SHA-2 includes algorithms such as SHA-224, SHA-256, and SHA-512.
- 🔒 The lab identifies GPG as a stronger method for ensuring downloaded files have not been modified by third parties.

---

# 🛡️ Cybersecurity Relevance

Hashing is an important concept in cybersecurity because it provides a way to identify changes to data.

In security operations and digital forensics, hashes can be useful for:

- File integrity verification
- Identifying modified files
- Comparing downloaded files with published hashes
- Supporting malware and forensic analysis
- Validating that data has not changed between comparisons

---

<div align="center">

### 🔐 Hashing • OpenSSL • SHA-256 • SHA-512 • File Integrity

**Cisco Networking Academy — CyberOps Workstation Lab**

</div>
