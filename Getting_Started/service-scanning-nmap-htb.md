---
title: "Getting Started with Service Scanning (Hack The Box)"
description: "Learn how to discover services with Nmap and enumerate FTP and SMB. Includes hands-on exercises for Hack The Box or authorised labs."
author: "Deep"
date: 2025-09-26
tags: [hackthebox, nmap, smb, ftp, service-scanning, beginner]
layout: post
---

# Getting Started with Service Scanning (Hack The Box)

Security testing starts with understanding what's running on a target machine. In this short guide you'll learn what a *service* is, why services matter during a pentest, and how to use **Nmap**, plus quick notes on **FTP** and **SMB**. At the end there are practical exercises you can try on Hack The Box or in any authorised lab.

> **Always have permission.** Only scan and attack machines you own or have explicit authorization to test (Hack The Box machines, CTF labs, or written permission). Unauthorized scanning or probing is illegal.

---

## What is a Service?

A **service** is an application running on a machine that listens for network connections. Common examples:

- Web servers (HTTP) — usually ports **80**, **8080**, or **8000**  
- SSH — port **22**  
- FTP — port **21**  
- SMB — port **445** (sometimes **139**)

As a penetration tester, you discover services then look for weaknesses in them. A vulnerable service can be coerced into doing something it shouldn't (information disclosure, file upload, remote code execution), which may let you escalate access or get a shell.

---

## Nmap — the reconnaissance workhorse

**Nmap** is the standard tool for discovering open ports and identifying services. The most basic scan:

```bash
nmap <TARGET_IP>
```

By default this scans the 1,000 most common TCP ports. Nmap normally runs TCP scans unless you specify otherwise.

**Useful flags**

- `-sV` — service/version detection (tries to identify service and version)  
- `-sC` — run default NSE scripts (useful quick checks)  
- `-p <ports>` — scan specific port(s), e.g. `-p 80,443`  
- `-p-` — scan all 65,535 TCP ports  
- `-Pn` — treat host as online (skip host discovery/ping)  
- `--open` — show only open ports  
- `-oA <basename>` — save output to `.nmap`, `.gnmap`, and `.xml`

**Example**

```bash
nmap -sC -sV -p 8080 <TARGET_IP>
```

**Read the output carefully**

- **STATE** — `open`, `filtered`, `closed` (filtered usually means a firewall or IDS is blocking probes)  
- **SERVICE** — identified service name (e.g., `http`)  
- **VERSION** — version string from the service (when `-sV` succeeds)

If you obtain a banner or version string, Google it to find release dates, changelogs, and known CVEs.

---

## FTP (File Transfer Protocol)

**FTP** runs on port **21** and is used to upload/download files. Nmap can indicate whether anonymous FTP is allowed.

To connect with the standard FTP client:

```bash
ftp <TARGET_IP>
```

If anonymous login is allowed, use username `anonymous` (password is usually your email or blank). FTP supports commands like `ls`, `cd`, `get`, and `put`. You may find files such as `login.txt` or other sensitive data.

> Note: Prefer SFTP (SSH File Transfer Protocol) for secure file transfers in real environments.

---

## SMB (Server Message Block)

**SMB** is used on Windows for file shares and can contain sensitive files (user lists, credentials). SMB commonly runs on **port 445**.

Nmap has useful SMB NSE scripts for enumeration and OS identification:

```bash
nmap --script smb-os-discovery.nse -p 445 <TARGET_IP>
```

To list SMB shares and connect:

```bash
# list shares (anonymous)
smbclient -N -L //TARGET_IP

# list shares with username prompt
smbclient -L //TARGET_IP -U bob
```

To connect to a specific share using provided credentials:

```bash
# interactive smbclient (username bob, password Welcome1)
smbclient //TARGET_IP/users -U bob%Welcome1

# once connected
ls
cd flag
get flag.txt
```

---

## Telnet

**Telnet** is an unencrypted remote shell. Some CTF/legacy boxes run Telnet on non-default ports. To locate Telnet on non-standard ports:

```bash
nmap -p- --open <TARGET_IP>
# then
nmap -sV -p <found_port> <TARGET_IP>
```

---

## Exercises

1. **Nmap — version on 8080**  
   Run:
   ```bash
   nmap -sC -sV -p 8080 <GIVEN_IP>
   ```
   **Submit:** the version string reported by Nmap for port 8080 (format like `Apache httpd 2.4.41`).

2. **Find non-default Telnet port**  
   Run:
   ```bash
   nmap -p- --open <GIVEN_IP>
   # then use -sV on candidate ports
   nmap -sV -p <candidate_port> <GIVEN_IP>
   ```
   **Submit:** the port number where Telnet is running.

3. **SMB — list shares and retrieve a flag**  
   - List shares:
     ```bash
     smbclient -N -L //<GIVEN_IP>
     ```
   - Connect to `users` as `bob` (password: `Welcome1`):
     ```bash
     smbclient //<GIVEN_IP>/users -U bob%Welcome1
     ```
   - Inside `smbclient`:
     ```
     cd flag
     get flag.txt
     ```
   **Submit:** the contents of `flag.txt`.

---

## Quick cheat-sheet (one-liners)

- Scan 1000 common ports:
  ```bash
  nmap <TARGET_IP>
  ```
- Scan all TCP ports:
  ```bash
  nmap -p- <TARGET_IP>
  ```
- Version detection + default scripts:
  ```bash
  nmap -sC -sV <TARGET_IP>
  ```
- SMB OS discovery:
  ```bash
  nmap --script smb-os-discovery.nse -p 445 <TARGET_IP>
  ```
- Connect to SMB share:
  ```bash
  smbclient //<TARGET_IP>/share -U user%password
  ```


