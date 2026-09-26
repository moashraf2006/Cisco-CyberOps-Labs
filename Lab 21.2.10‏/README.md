<div align="center">

# 🔐 Lab - Encrypting and Decrypting Data Using OpenSSL

**Cisco Networking Academy | OpenSSL | AES-256-CBC | Encryption & Decryption**

<p>
  <img src="https://img.shields.io/badge/Tool-OpenSSL-721412?style=for-the-badge" alt="OpenSSL">
  <img src="https://img.shields.io/badge/Cipher-AES--256-blue?style=for-the-badge" alt="AES-256">
  <img src="https://img.shields.io/badge/Encoding-Base64-orange?style=for-the-badge" alt="Base64">
  <img src="https://img.shields.io/badge/Cisco-NetAcad-1BA0D7?style=for-the-badge" alt="Cisco NetAcad">
</p>

<p>
  Encrypt and decrypt a text message using OpenSSL and AES-256-CBC, and examine the role of Base64 encoding.
</p>

</div>

---

## 🎯 Objectives

| Part | Objective |
|---|---|
| **Part 1** | Encrypting Messages with OpenSSL |
| **Part 2** | Decrypting Messages with OpenSSL |

---

## 🧰 Required Resources

- 💻 CyberOps Workstation virtual machine
- 🖥️ Terminal
- 🔐 OpenSSL
- 📝 `nano` / `cat`
- 📄 `letter_to_grandma.txt`

---

# 🧠 Background

OpenSSL is an open-source project that provides a full-featured toolkit for TLS and SSL protocols and also functions as a general-purpose cryptography library.

In this lab, OpenSSL is used to:

- Encrypt a text file using **AES-256-CBC**
- Encode encrypted output using **Base64**
- Decrypt the encrypted message
- Understand the difference between encrypted binary data and Base64-encoded output

> ⚠️ **Important Lab Warning**
>
> The method demonstrated in this lab is intended for **instructional purposes only** and should **not** be used to protect truly sensitive data.
>
> The lab identifies two security limitations:
>
> 1. The method uses a **weak key derivation function** and relies on a very strong password for security.
> 2. The method does **not guarantee the integrity** of the encrypted text file.

---

# 🔒 Part 1 — Encrypting Messages with OpenSSL

## 1. Navigate to the Lab Files

Log into the CyberOps Workstation VM and open a terminal.

Change to the lab directory:

```bash
cd ./lab.support.files/
```

Display the original text:

```bash
cat letter_to_grandma.txt
```

The file contains a letter to Grandma.

---

## 2. Encrypt the Text File

Use OpenSSL with AES-256-CBC:

```bash
openssl aes-256-cbc -in letter_to_grandma.txt -out message.enc
```

OpenSSL prompts for a password:

```text
enter aes-256-cbc encryption password:
Verifying - enter aes-256-cbc encryption password:
```

### 🔑 Password

The password is chosen by the student.

> **Note:** The same password must be remembered because it is required to decrypt the file.

---

## 3. Examine the Encrypted File

Display the encrypted file:

```bash
cat message.enc
```

### 🔎 Observation

The contents do not display as normal readable text.

Instead, the terminal displays symbols and unreadable characters.

This occurs because the encrypted output is generated as **binary data**.

```text
Plain Text
    │
    ▼
AES-256-CBC
    │
    ▼
Binary Encrypted Data
```

---

# 🔤 4. Encode the Encrypted Data with Base64

To make the encrypted output easier to represent as text, use the `-a` option:

```bash
openssl aes-256-cbc -a -in letter_to_grandma.txt -out message.enc
```

OpenSSL will again request the encryption password.

Display the new file:

```bash
cat message.enc
```

The output is now represented as Base64 text.

Example:

```text
U2FsdGVkX19ApWyrn8RD5zNp0RPCuMGZ98wDc26u/vmj1zyDXobGQhm/dDRZasG7
rfnth5Q8NHValEw8vipKGM66dNFyyr9/hJUzCoqhFpRHgNn+Xs5+TOtz/QCPN1bi
08LGTSzOpfkg76XDCk8uPy1hl/+Ng92sM5rgMzLXfEXtaYe5UgwOD42U/U6q73pj
...
```

> 💡 **Important:** Base64 encoding does **not** encrypt the data. It represents binary data using an ASCII string format.

---

## 📊 Binary vs Base64 Output

| Output | Appearance | Purpose |
|---|---|---|
| Binary encrypted output | Unreadable symbols | Encrypted data |
| Base64-encoded output | Readable text characters | Easier representation and transfer |

A benefit of Base64 encoding is that the encrypted message can be copied and pasted into places such as an email message.

---

# 🔓 Part 2 — Decrypting Messages with OpenSSL

OpenSSL can decrypt the encrypted file using a similar command.

## 1. Decrypt the Message

Run:

```bash
openssl aes-256-cbc -a -d -in message.enc -out decrypted_letter.txt
```

OpenSSL requests the password used during encryption.

Enter the same password.

The decrypted content is saved as:

```text
decrypted_letter.txt
```

---

## 2. Display the Decrypted Message

Use:

```bash
cat decrypted_letter.txt
```

### ✅ Result

The lab confirms that the letter is decrypted correctly.

The original readable message is restored.

---

# 🔄 Understanding the `-a` Option

The decryption command also includes:

```text
-a
```

This is necessary because `message.enc` was Base64-encoded after encryption.

The process therefore becomes:

```text
Encryption
───────────

Plain Text
    │
    ▼
AES-256-CBC Encryption
    │
    ▼
Encrypted Binary Data
    │
    ▼
Base64 Encoding
    │
    ▼
message.enc
```

And during decryption:

```text
Decryption
───────────

message.enc
    │
    ▼
Base64 Decoding
    │
    ▼
Encrypted Binary Data
    │
    ▼
AES-256-CBC Decryption
    │
    ▼
decrypted_letter.txt
```

---

# 🧪 Command Reference

## Encrypt

```bash
openssl aes-256-cbc -in letter_to_grandma.txt -out message.enc
```

## Encrypt + Base64 Encode

```bash
openssl aes-256-cbc -a -in letter_to_grandma.txt -out message.enc
```

## Decrypt + Base64 Decode

```bash
openssl aes-256-cbc -a -d -in message.enc -out decrypted_letter.txt
```

## Display Files

```bash
cat letter_to_grandma.txt
cat message.enc
cat decrypted_letter.txt
```

---

# 🔐 Encryption Workflow

```text
┌──────────────────────┐
│  letter_to_grandma   │
│      .txt            │
└──────────┬───────────┘
           │
           ▼
   ┌───────────────┐
   │  AES-256-CBC  │
   └───────┬───────┘
           │
           ▼
   ┌────────────────┐
   │ Encrypted Data │
   └───────┬────────┘
           │
           ▼
   ┌───────────────┐
   │    Base64     │
   └───────┬───────┘
           │
           ▼
     ┌───────────┐
     │message.enc│
     └───────────┘
```

---

# 🔓 Decryption Workflow

```text
┌───────────┐
│message.enc│
└─────┬─────┘
      │
      ▼
┌───────────────┐
│ Base64 Decode │
└───────┬───────┘
        │
        ▼
┌────────────────┐
│ Encrypted Data │
└───────┬────────┘
        │
        ▼
┌───────────────┐
│  AES-256-CBC  │
└───────┬───────┘
        │
        ▼
┌──────────────────────┐
│ decrypted_letter.txt │
└──────────────────────┘
```

---

# 🧠 Key Takeaways

- 🔐 OpenSSL can perform encryption and decryption from the command line.
- 🔒 This lab uses **AES-256-CBC** for encryption.
- 🔑 A password is required to encrypt and decrypt the message.
- 🧬 Encryption produces encrypted binary data.
- 🔤 Base64 converts binary data into an ASCII string representation.
- 📧 Base64 can make encrypted data easier to copy and transfer.
- 🚫 Base64 is **encoding, not encryption**.
- 🔄 The `-a` option is used to handle Base64 encoding/decoding.
- ⚠️ The lab's demonstrated method does not guarantee file integrity.
- 🛑 The demonstrated method should not be used for truly sensitive data.

---

# 🛡️ Cybersecurity Relevance

Understanding encryption from the command line is useful for cybersecurity students and analysts because it demonstrates the relationship between:

- Cryptographic algorithms
- Encryption and decryption
- Password-based protection
- Binary encrypted data
- Encoding formats
- Data confidentiality
- Data integrity limitations

The lab also highlights an important security principle: **encryption alone does not necessarily provide integrity protection**.

---

<div align="center">

### 🔐 OpenSSL • AES-256-CBC • Base64 • Cryptography

**Cisco Networking Academy — CyberOps Workstation Lab**

</div>
