---
title: "Getting Started (Hack The Box)"
description: "Introduction to essential tools (SSH, Netcat) and a hands-on banner-grabbing exercise using Netcat. Ready for GitHub: markdown, screenshots, and notes."
author: "<Deep>"
date: 2025-09-26
tags: [hackthebox, nmap, netcat, ssh, beginner, writeup]
layout: post
---

# Getting Started (Hack The Box)

Security work often begins with simple, reliable tools. This short guide introduces three essentials — **SSH**, **Netcat (nc)**, and quick tips for using them — plus a hands-on exercise: grab a service banner with Netcat.

> **Note:** Only run the examples in authorised lab environments (Hack The Box, local VMs, or systems you own). Unauthorized scanning or probing is illegal and unethical.

---

## Why these tools matter

Tools like **ssh**, **netcat (nc)**, **tmux**, and **vim** are used daily by security professionals. They're lightweight, available on most systems, and extremely versatile for remote access, service interaction, and note-taking during engagements.

Keep an offline copy of commands and outputs (notes) — it will save you hours when writing reports or reproducing findings.

---

## SSH (Secure Shell)

**SSH** is a secure network protocol that commonly runs on **port 22**. It provides encrypted remote login and command execution.

Two common authentication methods:

- **Password authentication** — enter a username and password.
- **Public key authentication** — use an SSH key pair (no password required on login).

SSH uses a client–server model. With valid credentials you can connect like this:

```bash
ssh username@<TARGET_IP>
```

If you need to specify a non-standard port:

```bash
ssh -p 2222 username@<TARGET_IP>
```

SSH is more stable and feature-rich than a raw reverse shell: it supports port forwarding, file transfer (scp/sftp), agent forwarding, and stronger session controls.

---

## Netcat (nc)

**Netcat** is a powerful tool for connecting to arbitrary TCP/UDP ports and interacting with services. It’s often called the “Swiss Army knife” for networking.

Basic usage to connect to a TCP port:

```bash
nc <TARGET_IP> <PORT>
```

Useful flags you might use when troubleshooting:

- `-v` — verbose (shows connection messages)
- `-n` — numeric-only (skip DNS lookup)
- `-w <seconds>` — timeout

Example (connect to port 22 to read the SSH banner):

```bash
nc -vn <TARGET_IP> 22
```

A typical SSH banner you might see:

```
SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.3
```

The banner gives you the protocol version and server implementation. That string is useful for fingerprinting and searching for version-specific issues (patches, CVEs).

---

## Quick exercise — grab a banner with Netcat

**Task:** Use Netcat to grab the service banner of a target server and submit the banner string as the answer.

**Command:**

```bash
nc -vn <TARGET_IP> <PORT>
```

**Example:** to grab an SSH banner on port 22:

```bash
nc -vn IP_ADDR
```

Copy the full banner line (it usually starts with `SSH-2.0-...`) and include it in your notes.

---

