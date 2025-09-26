---
title: "Nibbles — Enumeration (Hack The Box)"
description: "Enumeration walkthrough for the Nibbles box: assessment types, Nmap commands, note-taking checklist, and a short exercise."
author: "Deep"
date: 2025-09-27
tags: [hackthebox, nmap, enumeration, nibbles]
layout: post
---

# Nibbles — Enumeration (Hack The Box)

Before we start the box, here's a quick recap of assessment types you'll encounter.

- **Black-Box** — No or very little prior knowledge of the target. The pentester performs in-depth scanning to learn about the target (external testing, company → IP). No internal network knowledge is given.
- **Grey-Box** — The pentester receives certain information in advance (an IP address or low-level credentials). This simulates a malicious insider or evaluates what limited access can do.
- **White-Box** — Root-level access and source code are provided. This assessment is highly comprehensive since the tester has access to both sides of the target.

---

## Nmap

We start enumeration with an Nmap scan to discover which ports are open:

```bash
nmap -sV --open -oA nibbles_initial_scan <IP_ADDRESS>
```

This writes XML, greppable, and text output for later use. Proper note-taking is essential in real-world scenarios.

For web-focused enumeration:

```bash
nmap -sV --script=http-enum -oA nibbles_nmap_http_enum <IP_ADDRESS>
```

---

## What to capture in your notes

- Open ports and services (port/service/version).  
- Any unusual ports (non-standard web ports, admin panels).  
- Web endpoints discovered by `http-enum`.  
- Output snippets (copy lines showing version strings and banner info).  
- Screenshots of interesting web pages or error messages.

**Tip:** Keep an index file (e.g., `nibbles_notes.md`) and paste command outputs as you go. Use timestamps.

---

## Exercise

Run an Nmap script scan on the target. What is the **Apache version** running on the server? (answer format: `X.X.XX`)

- This is an easy exercise — all that's needed is an Nmap scan:
```bash
nmap -sV --script=http-enum -p 80 <IP_ADDRESS>
```

