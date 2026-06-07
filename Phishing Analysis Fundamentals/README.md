# TryHackMe — Phishing Emails 1 Write-Up

> **Room:** [Phishing Emails 1](https://tryhackme.com/room/phishingemails1tryoe)  
> **Module:** Phishing Analysis — SOC Level 1 Path  
> **Difficulty:** Easy  
> **Status:** Completed ✅  
> **Author:** [Dannyz15](https://github.com/Dannyz15)

---

## Table of Contents

- [Overview](#overview)
- [Task 1 — Introduction](#task-1--introduction)
- [Task 2 — The Email Address](#task-2--the-email-address)
- [Task 3 — Email Delivery](#task-3--email-delivery)
- [Task 4 — Email Headers](#task-4--email-headers)
- [Task 5 — Email Body](#task-5--email-body)
- [Task 6 — Types of Phishing](#task-6--types-of-phishing)
- [Task 7 — Conclusion](#task-7--conclusion)
- [Key Takeaways](#key-takeaways)
- [Further Learning](#further-learning)

---

## Overview

Spam and phishing remain the most common **social engineering** threats facing modern organizations. While spam is often low-risk, phishing can trick users into disclosing sensitive information or unknowingly deploying malware.

In this room, I explored the anatomy of a phishing email — from how email addresses are structured, how messages travel across networks, to how defenders analyze raw headers and bodies to identify attacker intent.

### Learning Objectives

- Learn the basics of email delivery
- Explore email header analysis
- Investigate and analyze email bodies
- Learn about the different types of phishing
- Analyze emails to identify potential security threats

---

## Task 1 — Introduction

The lab environment provides an `Email Samples` folder on the target machine's desktop. This task sets the stage for learning phishing analysis from a defender's perspective.

| Question | Answer |
|----------|--------|
| I am ready to learn about phishing analysis! | *No answer needed* |

---

## Task 2 — The Email Address

### Anatomy of an Email Address

An email address consists of three components:

```
username @ domain name
  david  @  tryhackme.com
  [1]   [2]     [3]
```

| Part | Role |
|------|------|
| **Username** | Identifies the specific recipient's mailbox on the email server |
| **@ Symbol** | Separates the username from the domain; tells the system where to route the email |
| **Domain Name** | Specifies the mail server responsible for receiving the message |

> **Analogy:** The domain is the apartment building; the username is the specific mailbox inside it.

### Questions & Answers

| Question | Answer |
|----------|--------|
| Identify the domain used in the following email address: `hatsalesman@tryhatme.com` | `tryhatme.com` |

---

## Task 3 — Email Delivery

### Core Email Protocols

| Protocol | Full Name | Purpose |
|----------|-----------|---------|
| **SMTP** | Simple Mail Transfer Protocol | Sends emails from a client to a mail server |
| **POP3** | Post Office Protocol v3 | Downloads emails to a **single** device |
| **IMAP** | Internet Message Access Protocol | Syncs emails across **multiple** devices |

### POP3 vs IMAP

| Feature | POP3 | IMAP |
|---------|------|------|
| Emails stored on | Single device | Server |
| Multi-device access | ❌ No | ✅ Yes |
| Syncs across devices | ❌ No | ✅ Yes |
| Emails removed after download | Usually yes | No (unless deleted) |

### An Email's Journey (Step by Step)

```
1. User sends email     → Client sends message to mail server via SMTP
2. DNS query            → Sending server asks DNS for recipient's mail server
3. DNS response         → DNS returns the address of the recipient's mail server
4. Email delivered      → Message is sent across the Internet to recipient's server
5. Recipient checks     → Recipient's email client connects to their mail server
6. Email retrieved      → Downloaded (POP3) or synced (IMAP) to the recipient's device
```

### Questions & Answers

| Question | Answer |
|----------|--------|
| Which protocol is responsible for sending an email from a client to a mail server? | `SMTP` |
| Which service is used to look up the recipient domain's mail server? | `DNS` |
| Bob wants to access his email from multiple devices, including his phone and laptop. Which protocol should he use? | `IMAP` |

---

## Task 4 — Email Headers

### What Are Email Headers?

Email headers contain **metadata** about the message — information about the sender, the servers involved in delivery, and timestamps. This is crucial when investigating suspicious emails.

### Key Header Fields

| Field | Description |
|-------|-------------|
| `From` | Sender's email address |
| `To` | Recipient's email address |
| `Reply-To` | Address where replies are sent (optional; can differ from `From`) |
| `Subject` | The email's subject line |
| `Date` | Date and time the email was sent |
| `X-Originating-IP` | **Reveals the true source IP address of the email** |

### Viewing the Message Source in Thunderbird

To view the full raw message (headers + body):

1. Open the email in **Thunderbird**
2. Click the **View** menu
3. Select **Message Source**
4. Or use the shortcut: `Ctrl + U`

This reveals all header fields, including the originating IP address and any authentication results.

### Questions & Answers (email1.eml)

| Question | Answer |
|----------|--------|
| What is the full subject line of `email1.eml`? | `Help protect your budget by protecting your home` |
| View the message source of `email1.eml`. What is the IP address listed as the `X-Originating-Ip`? | `43.255.56.161` |

---

## Task 5 — Email Body

### HTML vs Plain Text Emails

Emails can be rendered as **plain text** or **HTML formatted**. Viewing the raw HTML source reveals hidden links, embedded images, and other elements not visible in the standard rendered view.

### Reconstructing Attachments

Email attachments are embedded directly in the raw message. Important headers to look for:

| Header | What It Tells You |
|--------|-------------------|
| `Content-Type` | The MIME type of the file (e.g., `application/pdf`) |
| `Content-Disposition` | Confirms it's an attachment and reveals the **filename** |
| `Content-Transfer-Encoding` | Encoding method — typically `base64` |

### Decoding Base64 Attachments

The attachment content is stored as a **Base64-encoded** string. To reconstruct the file:
- Use **[CyberChef](https://gchq.github.io/CyberChef/)** — From Base64 → Save as file
- Use an online **Base64 to PDF converter**

### Questions & Answers (email2.txt)

| Question | Answer |
|----------|--------|
| Open `email2.txt`. What is the `Content-Type` of the attachment? | `application/pdf` |
| What is the name of the attachment? | `zmqpalgh.pdf` |
| Decode the base64 string. What is the hidden flag value? | `THM{BENIGN_PDF_ATTACHMENT}` |

---

## Task 6 — Types of Phishing

### Types of Phishing Attacks

| Type | Description |
|------|-------------|
| **Spam** | Unsolicited bulk emails sent to many recipients. A malicious form is called **malspam** |
| **Spear Phishing** | Targeted phishing at a specific individual or organization using personalized info |
| **Whaling** | Targets high-level executives (CEO, CFO) to steal sensitive data or initiate financial fraud |
| **Smishing** | Phishing conducted via SMS or text messages |
| **Vishing** | Phishing conducted via voice calls, exploiting social engineering over the phone |

### Anatomy of a Phishing Email

Common red flags to look for:

| Indicator | Description |
|-----------|-------------|
| 🔴 **Spoofed From Address** | Sender appears to be a trusted entity |
| 🔴 **Urgent Subject / Message** | Creates pressure ("Your account will be locked in 24 hours!") |
| 🔴 **Brand Impersonation** | Mimics a legitimate company's logo, color scheme, and tone |
| 🔴 **Grammar & Spelling Issues** | Errors or unnatural phrasing not typical of legitimate companies |
| 🔴 **Generic Content** | Uses "Dear Customer" instead of your actual name |
| 🔴 **Hidden / Shortened Links** | Hover text shows a legitimate domain but the actual URL is malicious |
| 🔴 **Malicious Attachments** | Files disguised as invoices, PDFs, or other legitimate documents |

### Safe Analysis — Defanging URLs & IPs

When analyzing malicious URLs or IPs, **defang** them to prevent accidental clicks or connections:

| | Example |
|-|---------|
| **Original IP** | `103.234.236.83` |
| **Defanged IP** | `103[.]234[.]236[.]83` |
| **Original URL** | `http://malicious.com` |
| **Defanged URL** | `hxxp[://]malicious[.]com` |

> Defanging replaces `.` with `[.]` and `http` with `hxxp` — making the value unclickable while still readable for analysis.

### Investigation — Phishing Email Analysis

The email analyzed in this task impersonates **Home Depot**, a well-known home improvement retail chain — a classic brand impersonation attack.

```
Spoofed Brand:     Home Depot
Sender Email:      support@teckbe.com   ← fake domain, not homedepot.com
X-Originating-IP:  103[.]234[.]236[.]83 (defanged)
Auth-Results From: atlas102.free.mail.gq1.yahoo.com
```

> ⚠️ **Key Observation:** The `Authentication-Results` header was generated by a Yahoo mail server (`gq1.yahoo.com`), which is completely unrelated to Home Depot — a clear sign of a spoofed/malicious email.

### Questions & Answers

| Question | Answer |
|----------|--------|
| Which reputable organization is being spoofed in this phishing attempt? | `Home Depot` |
| What is the sender's email address? | `support@teckbe.com` |
| Inspect the email message source. What is the defanged `X-Originating-IP`? | `103[.]234[.]236[.]83` |
| Which mail server generated the `Authentication-Results` header? | `atlas102.free.mail.gq1.yahoo.com` |

---

## Task 7 — Conclusion

### Business Email Compromise (BEC)

**Business Email Compromise (BEC)** is a sophisticated attack where an adversary:

1. Gains access to a **legitimate internal email account**
2. Uses that compromised account to send emails from **within** the organization
3. Tricks employees into performing **unauthorized or fraudulent actions** (e.g., wire transfers, credential submission)

BEC is especially dangerous because the email originates from a real, trusted account — bypassing many spam and phishing filters.

### Questions & Answers

| Question | Answer |
|----------|--------|
| What attack, signified by the acronym BEC, uses a compromised email to trick employees into fraud? | `Business Email Compromise` |

---

## Key Takeaways

| Concept | Summary |
|---------|---------|
| **Email Address Structure** | `username@domain` — three parts working together |
| **SMTP** | Protocol used to *send* emails from client to server |
| **DNS** | Looks up the mail server for the recipient's domain |
| **IMAP vs POP3** | IMAP syncs across devices; POP3 downloads to one device only |
| **Email Headers** | Reveal true sender IP, routing servers, authentication results |
| **X-Originating-IP** | Key header for identifying the true origin of an email |
| **Base64 Attachments** | Embedded in raw message; decoded with CyberChef |
| **Defanging** | Replace `http` → `hxxp` and `.` → `[.]` for safe analysis |
| **BEC** | Attacker uses a *real* compromised account — harder to detect |

### Tools Used

| Tool | Purpose |
|------|---------|
| **Thunderbird** | View raw email source (headers + body) via `Ctrl+U` |
| **CyberChef** | Decode Base64-encoded attachments |
| **Defanging** | Safely handle malicious URLs and IPs during analysis |

---

## Further Learning

This room is the first in the **Phishing Analysis** module of the **SOC Level 1 Path**.

| Room | Link |
|------|------|
| Phishing Emails in Action | [TryHackMe](https://tryhackme.com/room/phishingemails2rytmuv) |
| Phishing Analysis Tools | [TryHackMe](https://tryhackme.com/room/phishingemails3ncoivu4) |
| Phishing Prevention | [TryHackMe](https://tryhackme.com/room/phishingemails4gkxh) |
| The Greenholt Phish | [TryHackMe](https://tryhackme.com/room/phishingemails5fgjlzxc) |
| Snapped Phish-ing Line | [TryHackMe](https://tryhackme.com/room/snappedphishingline) |

---

*Write-up by [Dannyz15](https://github.com/Dannyz15)*  
*TryHackMe: [tryhackme.com/p/Dannyz15](https://tryhackme.com/p/Dannyz15)*