---
title: "Secure Document Vault: Hybrid Encryption"
date: 2026-02-02
description: "My first cryptography project as an Informatics student, implementing a secure backend with AES-256 and RSA-2048."
summary: "Exploring the balance between speed and security. A backend system that uses Hybrid Cryptography to handle secure batch document uploads without compromising performance."
tags: ["Cryptography", "Backend", "Node.js", "Research", "Security"]
showTableOfContents: true
---

As an Informatics student, the curriculum covers a vast array of fields rather than specializing in one. My journey has been diverse—I have built **Mobile Apps**, **Web Apps**, developed **3D Vector Graphics simulations**, and even authored **Statistical Research papers**.

However, since discovering that **Cybersecurity** is my passion, I’ve been eager to explore it further. Now, after 2 years in college, I finally have the chance to take a subject dedicated to security. This project serves as a showcase of that subject, moving beyond general programming into secure system architecture.

---

## 🎯 The Goal

In modern cloud storage, we often trade security for convenience. My goal was to build a **"Secure Document Vault"** backend that ensures:
1.  **Confidentiality:** Files stored on the disk must be unreadable without the key.
2.  **Integrity:** The system must detect if a file has been tampered with.
3.  **Performance:** It must handle **Batch Processing** (uploading multiple files simultaneously) efficiently.

The technical challenge was combining **AES-256** (fast but hard to manage keys) with **RSA-2048** (secure keys but slow for data) into a seamless **Hybrid Model**.

---

## 🛠️ What Did I Do?

I engineered a REST API backend that acts as a secure vault. Instead of relying on a single encryption method, I built a layered security architecture.

### The Tech Stack
* **Runtime:** Node.js (v18) & Express.js
* **Database:** MongoDB (storing metadata like hash & wrapped keys)
* **Cryptography:** AES-256 (GCM & CBC modes), RSA-2048, SHA-256
* **Testing:** Postman & Custom Scripting

### The Hybrid Mechanism
Here is how I solved the speed vs. security trade-off:
1.  The file itself is encrypted using **AES-256**. This is incredibly fast.
2.  The ephemeral AES key (used only once) is then encrypted using the server's **RSA Public Key**.
3.  I generated a **SHA-256 hash** of the original file to detect any tampering during storage.

{{< alert icon="circle-info" >}}
**Why Hybrid?** It allows us to process gigabytes of data with the speed of AES, while keeping the keys locked away with the mathematical rigor of RSA.
{{< /alert >}}

---

## 🚀 The Process

This wasn't just about importing a crypto library; it was about architecture.

### Phase 1: Designing the Architecture
I started by mapping out how data flows from the user to the disk. I decided on a **Three-Tier Architecture** to separate the client, the logic (Crypto Engine), and the storage layer.

{{< figure src="resources/Architecture.png" alt="Secure Document Vault Architecture" caption="Figure 1: Global System Architecture & Hybrid Encryption Flow" >}}

### Phase 2: The Batch Processing Challenge
Handling one file is easy. Handling 12 files at once in a single HTTP request is where it gets tricky. I used `multipart/form-data` to split the request body, allowing the system to process a mix of PDFs, images, and text files simultaneously.

Each file in the batch gets its own unique AES key and IV, ensuring that if one file is compromised, the others remain secure.

### Phase 3: Benchmarking (The Fun Part)
I didn't just assume it worked; I stress-tested it. I compared two AES modes: **CBC (Cipher Block Chaining)** vs. **GCM (Galois/Counter Mode)**.

I ran tests with various datasets:
* **Small Batch:** 3 mixed files.
* **Medium Batch:** 5 files (including dummy sensitive data).
* **Large Batch:** 12 files (stress test).

{{< figure src="resources/upload-batch.png" alt="Secure Document Vault Architecture" caption="Figure 2: Successful Batch Upload using Postman" >}}

---

## 📊 The Outcome

The results were surprisingly good. The system handled the "Large Batch" scenario (12 files) in just **100-148 ms**.

### Key Findings:
Encrypting the keys with RSA took only **1.56%** of the total processing time (approx. 2ms). The fear that asymmetric encryption would slow down the system was debunked.

  * **AES-CBC** was slightly faster in raw encryption speed (83 MB/s).
  * **AES-GCM** provided better security (Authenticated Encryption) and had **0 bytes of storage overhead**.

| Metric | AES-CBC | AES-GCM | Winner |
| :--- | :--- | :--- | :--- |
| **Speed** | 20 ms | 24 ms | CBC |
| **Overhead** | 16 bytes | 0 bytes | **GCM** |
| **Security** | Standard | Authenticated | **GCM** |

*(Data derived from my benchmarks on 1.7MB files)*

---

## 💡 Lessons Learned

Completing this project taught me that **security is about architecture, not just algorithms.**

* Using standard, well-tested algorithms (AES/RSA) in a smart architecture is far better than trying to invent new ones.
* Implementing SHA-256 saved me from corrupted data multiple times during testing. It’s a simple check that adds massive reliability.
* Diving deep into Node.js streams and encryption modes allowed me to optimize the batch processing to be nearly instant.

It was a steep learning curve, but seeing the "200 OK" green status on a batch encryption request was incredibly satisfying.

---

For a deep dive into the technical details, you can read my full report below:

{{< button href="resources/sdv-report.pdf" target="_blank" >}}
  {{< icon "file-lines" >}} Full Report (PDF)
{{< /button >}}