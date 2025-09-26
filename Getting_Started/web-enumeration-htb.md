---
title: "Getting Started with Web Enumeration (Hack The Box)"
description: "Web enumeration techniques for CTFs and labs: directory brute-forcing, banner grabbing, subdomain discovery, robots.txt, and certificate checks. Includes practical Gobuster and curl examples."
author: "Deep"
date: 2025-09-26
tags: [hackthebox, web-enumeration, gobuster, curl, reconnaissance, beginner]
layout: post
---

# Getting Started with Web Enumeration (Hack The Box)

Web enumeration is the process of discovering everything about a target web application that can help you exploit it: running services, directories and files, subdomains, server headers, certificates, `robots.txt`, and the site source. In labs or CTFs this information often leads to hidden pages, credentials, or an RCE path.

> **Always have permission.** Only run these techniques against machines you own or are authorised to test (Hack The Box, authorised labs, or written permission). Unauthorized scanning or brute-forcing is illegal.

---

## Common web ports

Servers running web applications typically use:

- **HTTP** — port **80** (also **8080**, **8000** for alternate servers)  
- **HTTPS** — port **443**

If an organization doesn't expose many services, the web layer often contains the most valuable attack surface.

---

## Gobuster — directory and file discovery

After Nmap reveals web ports, check for hidden files and directories. Tools like **Gobuster** expose paths that aren't intended for public access and can reveal admin panels, backups, or upload points.

```bash
# directory brute-force example
gobuster dir -u http://<TARGET_IP>/ -w /path/to/SecLists/Discovery/Web-Content/common.txt -t 50 -x php,html,txt
```

Gobuster can also discover vhosts, DNS subdomains, and S3 buckets:

```bash
# vhost discovery
gobuster vhost -u http://<TARGET_IP> -w /path/to/wordlist.txt

# DNS/subdomain discovery
gobuster dns -d example.com -w /path/to/wordlist.txt
```

**Tips**
- Use SecLists: `Discovery/Web-Content/common.txt`, `raft-large-directories.txt`.
- Adjust threads (`-t`) based on target scope and politeness.
- Add file extensions with `-x` to find `index.php`, `backup.zip`, etc.

**Interpreting status codes**
- `200` — resource exists (good lead)  
- `301/302` — redirect (follow)  
- `403` — forbidden (resource exists but blocked)  
- `401` — requires authentication  
- `500` — server error (may leak stack traces)

---

## Enumerating subdomains

Subdomains can host admin panels, staging sites, or forgotten services. Use Gobuster's DNS mode or tools like `amass` for deeper enumeration:

```bash
gobuster dns -d example.com -w /path/to/wordlist.txt
# or use amass for advanced enumeration
amass enum -d example.com
```

Certificate transparency logs (CRT.sh) and services like Censys can also reveal subdomains from issued certificates.

---

## Banner grabbing / headers (cURL)

HTTP headers provide quick intel about the server, framework, and authentication methods.

```bash
# fetch headers only (follow redirects)
curl -IL https://<TARGET_HOST>
```

Look for:
- `Server:` (webserver and version)  
- `X-Powered-By:` (framework/language)  
- `WWW-Authenticate:` (auth scheme)

These clues help you search for relevant CVEs and misconfigurations.

---

## Certificates

TLS certificates can reveal subdomains, company names, and contact emails. Use `openssl` to view certificate info:

```bash
echo | openssl s_client -connect <TARGET_HOST>:443 -servername <TARGET_HOST> 2>/dev/null   | openssl x509 -noout -subject -issuer -dates -text
```

Certificate transparency logs (CRT.sh) are useful for OSINT-style discovery.

---

## Robots.txt

`/robots.txt` instructs crawlers what to index — and sometimes lists paths the owner doesn't want indexed (which can be juicy).

```bash
curl -s http://<TARGET_IP>/robots.txt
```

Treat entries as prioritized enumeration targets; they may be accessible even if listed as disallowed.

---

## Source code & client-side files

Check page source, JavaScript, and linked assets for:
- Hard-coded endpoints or API keys  
- Commented credentials or TODOs  
- Feature flags or staging endpoints

Use browser DevTools or `curl`/`wget`:

```bash
curl -s http://<TARGET_IP>/somepage | sed -n '1,200p'
wget --mirror --no-parent http://<TARGET_IP>/
```

---

## Example workflow

1. Run Nmap to identify web ports:
   ```bash
   nmap -sC -sV -p- <TARGET_IP>
   ```
2. Check tech fingerprint:
   ```bash
   whatweb http://<TARGET_IP>
   ```
3. Run Gobuster on discovered web ports:
   ```bash
   gobuster dir -u http://<TARGET_IP>:8080/ -w /path/to/common.txt -t 50 -x php,html,txt
   ```
4. Check `robots.txt`:
   ```bash
   curl -s http://<TARGET_IP>:8080/robots.txt
   ```
5. Inspect page source and JS for hidden endpoints.
6. If you find upload points or admin panels, enumerate them carefully (file types, upload paths, auth).

---

## Wordlists & tools

- Wordlists: **SecLists** (`Discovery/Web-Content/`, `raft`, `common.txt`)  
- Tools: `gobuster`, `ffuf`, `whatweb`, `curl`, `wget`, `burpsuite`, `amass`

---

## Practical exercise

Try these steps on the lab target and use the results to find the flag:

1. `nmap -sC -sV -p- <TARGET_IP>` to identify web ports.  
2. Run Gobuster on the discovered web service:
   ```bash
   gobuster dir -u http://<TARGET_IP>:<PORT>/ -w /path/to/common.txt -t 50 -x php,html,txt
   ```
3. Check `robots.txt`:
   ```bash
   curl -s http://<TARGET_IP>:<PORT>/robots.txt
   ```
4. Inspect page source and JS files for hidden endpoints or credentials.
5. If you find an admin panel or upload functionality, enumerate further (file upload restrictions, LFI/RFI patterns, parameter fuzzing).
6. When you find `flag.txt` or the flag location, retrieve it and submit the contents.

---

## Quick checklist before you run

- [ ] Confirm scope and permission  
- [ ] Choose appropriate wordlist size (don’t overload remote server)  
- [ ] Use polite threading and rate limits for public targets  
- [ ] Record all findings (screenshots, commands, outputs)

---


