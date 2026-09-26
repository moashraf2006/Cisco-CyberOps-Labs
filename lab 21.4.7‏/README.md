# 🔐 Certificate Authority Stores & HTTPS MITM Detection

<p align="center">
  <b>CyberOps / Network Security Lab</b><br>
  Certificate Analysis • TLS Inspection • MITM Detection • OpenSSL
</p>

---

## 📌 Overview

This lab explores how **Certificate Authorities (CAs)** establish trust in HTTPS connections and how **certificate fingerprints** can be used to identify possible HTTPS interception.

The lab covers:

* 🔎 Inspecting trusted Root CAs
* 🔐 Understanding HTTPS/TLS certificate validation
* 🕵️ Understanding HTTPS proxy interception
* 🧮 Extracting certificate fingerprints
* 🛠️ Using OpenSSL for certificate analysis
* 🚨 Detecting possible TLS/HTTPS MITM interception

---

## 🎯 Objectives

* Identify certificates trusted by a web browser.
* Understand the role of Root CAs and child CAs.
* Understand how HTTPS proxies can inspect encrypted traffic.
* Extract certificates using OpenSSL.
* Calculate SHA-1 certificate fingerprints.
* Compare fingerprints to identify possible certificate interception.
* Understand the limitations of fingerprint-based detection.

---

## 🧰 Environment

| Component      | Used                    |
| -------------- | ----------------------- |
| OS             | CyberOps Workstation VM |
| Browser        | Chrome / Firefox        |
| Protocol       | HTTPS / TLS             |
| Tool           | OpenSSL                 |
| Hash Algorithm | SHA-1                   |

---

# 1️⃣ Trusted Certificate Authorities

HTTPS relies on trusted **Certificate Authorities** to validate the identity of websites.

Browsers and operating systems maintain a certificate store containing trusted CAs. If a website certificate is signed by a trusted CA, the browser can establish trust in that certificate.

### Chrome

```text
Settings
   ↓
Privacy & Security
   ↓
Manage Certificates
   ↓
Trusted Root Certification Authorities
```

### Firefox

```text
Settings / Preferences
   ↓
Privacy & Security
   ↓
Security
   ↓
View Certificates
```

The lab demonstrates these certificate-store locations for examining trusted certificates.

---

# 2️⃣ HTTPS MITM / Proxy Concept

An organization can deploy an **HTTPS proxy** capable of inspecting TLS traffic.

For transparent interception, the client must trust the proxy's CA certificate.

```text
┌──────────────┐
│    Client    │
└──────┬───────┘
       │
       │ TLS
       ▼
┌──────────────┐
│ HTTPS Proxy  │
└──────┬───────┘
       │
       │ TLS
       ▼
┌──────────────┐
│ Target Site  │
└──────────────┘
```

The proxy terminates the client's TLS connection, establishes another TLS connection with the destination, and can inspect traffic between the two connections.

---

# 3️⃣ Certificate Fingerprints

A certificate contains a **fingerprint**, which is a hash-based summary of the certificate.

Because changing certificate contents changes the resulting fingerprint, fingerprints can be used to compare certificates.

```text
Expected Certificate
        │
        ▼
   Fingerprint
        │
        │
        ▼
      Compare
        ▲
        │
        │
Received Certificate
        │
        ▼
   Fingerprint
```

A mismatch can indicate that a different certificate was presented to the client.

---

# 4️⃣ Extracting the Certificate

Connect to Cisco's HTTPS service and extract the certificate:

```bash
echo -n | openssl s_client -connect cisco.com:443 | \
sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > ./cisco.pem
```

### What the command does

| Command                  | Purpose                        |
| ------------------------ | ------------------------------ |
| `openssl s_client`       | Establishes a TLS connection   |
| `-connect cisco.com:443` | Connects to HTTPS port 443     |
| `sed`                    | Extracts the certificate block |
| `>`                      | Saves the output to a file     |
| `cisco.pem`              | Stores the certificate         |

The lab uses this process to retrieve the certificate presented by Cisco's HTTPS service.

---

# 5️⃣ Inspect the Certificate

```bash
cat cisco.pem
```

The certificate is stored in PEM format:

```text
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
```

---

# 6️⃣ Calculate the SHA-1 Fingerprint

```bash
openssl x509 -noout -in cisco.pem -fingerprint -sha1
```

Example:

```text
SHA1 Fingerprint=64:19:CA:40:E2:1B:3F:92:29:21:A9:CE:60:7D:C9:0C:39:B5:71:3E
```

The lab specifically uses **SHA-1** so the resulting fingerprint can be compared with the SHA-1 reference fingerprints provided in the exercise.

> ⚠️ Certificate fingerprints may change when certificates are renewed, so the reference values in the original lab should not automatically be considered current.

---

# 7️⃣ Fingerprint Comparison

### Match

```text
Expected Fingerprint
        =
Received Fingerprint
        ↓
Certificate matches
```

### Mismatch

```text
Expected Fingerprint
        ≠
Received Fingerprint
        ↓
Possible certificate interception
```

According to the lab, a non-matching fingerprint can indicate that an HTTPS proxy or another endpoint presented a different certificate.

---

# ⚠️ Limitations

Fingerprint comparison is **not a 100% foolproof detection method**.

The lab identifies several limitations:

* The VM may not contain an enterprise CA that is installed on other machines.
* An enterprise may intercept only selected websites using dynamic rules.
* Certificates are periodically renewed and therefore fingerprints can legitimately change.

Therefore:

> A fingerprint mismatch should be investigated and validated rather than automatically treated as proof of malicious interception.

---

# 🧠 Key Takeaways

```text
HTTPS
  │
  ▼
Certificate
  │
  ▼
Certificate Authority
  │
  ▼
Trusted CA Store
  │
  ▼
Certificate Validation
  │
  ▼
Fingerprint Comparison
  │
  ▼
Possible MITM Detection
```

### Core Concepts

* **CA** → Trusted authority responsible for certificate issuance.
* **Root CA** → Root of a trusted certificate chain.
* **TLS/HTTPS** → Provides encrypted communication.
* **HTTPS Proxy** → Can inspect TLS traffic when trusted by the client.
* **Fingerprint** → Hash-based representation of a certificate.
* **SHA-1** → Hash algorithm used for fingerprints in this lab.
* **MITM** → Interception between communicating endpoints.

---

# 🔬 Skills Demonstrated

`Certificate Analysis` `OpenSSL` `TLS` `HTTPS` `Network Security` `MITM Detection` `CyberOps` `Incident Analysis`

---

## 📚 Source

Cisco Networking Academy — **Lab: Certificate Authority Stores**

This README is based on the provided Cisco CyberOps lab material.
