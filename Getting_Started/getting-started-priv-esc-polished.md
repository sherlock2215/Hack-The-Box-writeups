---
title: "Getting Started with Privilege Escalation (Hack The Box)"
description: "Checklist and practical tips for local enumeration and privilege escalation techniques on Linux."
author: "Deep"
date: 2025-09-27
tags: [hackthebox, privilege-escalation, linpeas, linum, ssh-keys, sudo]
layout: post
---

# Getting Started with Privilege Escalation (Hack The Box)

Privilege escalation is the process of moving from a low-privileged account (the one you initially compromise) to a higher privileged account (often **root** on Linux). After you get an initial foothold, your next job is thorough local enumeration: find misconfigurations, exposed credentials, weak sudo rules, scheduled tasks, kernel or software vulnerabilities, and any other weaknesses that let you escalate.

> **Always have permission.** Only attempt privilege escalation on systems you own or are explicitly authorized to test (Hack The Box, labs, or written permission). Exploitation can crash systems and cause data loss.

---

## Checklist for Privilege Escalation

After gaining access to a box, thoroughly check and enumerate the system for vulnerabilities. Useful automated tools include:

- **LinEnum** (great all-round Linux enumeration):
  ```bash
  wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
  ```
- **PEASS** (Privilege Escalation Awesome Scripts SUITE)

Other important manual checks:
```bash
uname -a
cat /etc/os-release
lsb_release -a
```

---

## Kernel Exploits

Outdated kernels or software can provide local privilege escalation opportunities. Search for public kernel exploits or use binary diffing to identify vulnerable code paths in installed software.

---

## User Privileges

Check the current user's privileges and available commands. The `sudo` command allows a user to run commands as another user (often root). Use `sudo -l` to list allowed commands and look for NOPASSWD rules or commands that can be abused to spawn a shell.

---

## Scheduled Tasks

Scheduled tasks run scripts at intervals (cron on Linux, Scheduled Tasks on Windows). Misconfigured cron jobs that execute writable scripts, or run programs from writable directories, can be abused to escalate privileges. Check common locations:
- `/etc/crontab`
- `/etc/cron.d`
- `/var/spool/cron/crontabs/root`

---

## Exposed Credentials

Search logs, user histories, bash history, and configuration files for exposed passwords and secrets. Files to check include application configs, `.env` files, and backup files.

---

## SSH Keys

If you can read another user's `.ssh` directory, you may find private keys such as `/home/user/.ssh/id_rsa` or `/root/.ssh/id_rsa`. If you obtain an `id_rsa` file, copy it to your machine and set proper permissions before using it:

```bash
chmod 600 id_rsa
ssh -i id_rsa user@<TARGET_IP> -p <PORT>
```

If you have write access to a target user's `.ssh` directory, you can place your public key there to gain access. Generate a keypair locally first:

```bash
ssh-keygen -f key
# results: key (private) and key.pub (public) to copy to the remote machine
```

---

## Exercise

SSH into the server above with the provided credentials, and use the `-p xxxxxx` flag to specify the port shown above. Once you log in, try to move to `user2` and retrieve the flag at `/home/user2/flag.txt`.

```bash
ssh user1@[IP] -p PORT
# check sudo privileges
sudo -l
# if allowed:
sudo -u user2 /bin/bash
# then
cat /home/user2/flag.txt
```

Once you gain access to `user2`, try to escalate to `root` and retrieve `/root/flag.txt`. Steps to consider:
- check what privileges and files you can access
- use SSH keys and copy them to your remote machine if available
- you may be able to connect as root using SSH or even netcat

---

