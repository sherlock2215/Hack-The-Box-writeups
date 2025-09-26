---
title: "Nibbles — Initial Foothold (Hack The Box)"
description: "How the initial foothold was obtained on Nibbleblog: admin discovery, plugin upload, and getting a shell."
author: "Deep"
date: 2025-09-27
tags: [hackthebox, nibbleblog, initial-foothold, web-exploit]
layout: post
---

# Nibbles — Initial Foothold (Hack The Box)

After logging in to Nibbleblog, the admin area exposes several pages:

- **Publish** — create new posts/pages (video, quote, etc.). Could be interesting.  
- **Comments** — shows no published comments.  
- **Manage** — manage posts, pages, and categories (edit/delete). Not overly interesting.  
- **Settings** — confirms the vulnerable version **4.0.3**. Several settings exist but none appeared valuable at first glance.  
- **Themes** — install a new theme from a pre-selected list.  
- **Plugins** — configure, install, or uninstall plugins. The **My image** plugin allows image uploads.

---

## Enumeration & discovery

We used **Nmap** and **Gobuster** to find interesting pages and confirm services:

```bash
gobuster dir -u http://[IP]/nibbleblog/ -w=/usr/share/dirb/wordlists/common.txt
nmap -sVC [IP]
```

Gobuster discovered an `admin.php` page. In the `content` folder we found `users.xml`, which provided the `admin` username. After a bit of brute-forcing I found the `admin` password: **nibbles**.

---

## Abusing the image upload plugin

The **My image** plugin allows file uploads. The plugin is vulnerable and can be abused to upload server-side code. I uploaded a PHP file that executed a reverse shell and started a listener on my machine. When I visited the uploaded file in the browser, the listener immediately connected and provided an interactive shell.

> **Note:** The exact webshell payload has been removed from this public write-up.

---

## Flow to obtain the shell

1. Log in to the admin panel with the discovered credentials (`admin:nibbles`).  
2. Navigate to **Plugins → My image** and upload the PHP file.  
3. Start a listener on the chosen port on your machine.  
4. Trigger the uploaded file by visiting its URL in the browser. The listener connects and an interactive shell is obtained.  
5. From the shell, retrieve the flag.

---

## Final note

With the interactive shell from the webshell upload, you can proceed to enumerate the machine and capture the flag:)
