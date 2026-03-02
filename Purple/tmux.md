# TryHackMe - RoomName Writeup

**Difficulty:** Easy  
**Date:** 27-02-2026  
**Author:** YourName  

---

## Summary
Quick room about basic enumeration and exploiting a misconfigured service to get user and root flags.

---

## Enumeration

### Nmap Scan
```bash
nmap -sV -sC -p- 10.10.x.x
```
Found open ports:
- 22 (SSH)
- 80 (HTTP)
- 8080 (HTTP)

### Web Enumeration
Visited the site on port 80, found a basic login page.
Ran gobuster to find hidden directories:
```bash
gobuster dir -u http://10.10.x.x -w /usr/share/wordlists/dirb/common.txt
```
Found `/admin` directory.

---

## Exploitation

Tried default credentials `admin:admin` on the login page — it worked.  
Inside the admin panel, found a file upload feature. Uploaded a PHP reverse shell.

Set up listener:
```bash
nc -lvnp 4444
```
Got a shell as `www-data`.

---

## Privilege Escalation

Ran `sudo -l` and found:
```
(ALL) NOPASSWD: /usr/bin/vim
```
Used GTFOBins vim exploit to escalate to root.

---

## Flags

| Flag | Value |
|------|-------|
| User | THM{xxxxxxxxxxxx} |
| Root | THM{xxxxxxxxxxxx} |

---

## Lessons Learned
- Always check for default credentials
- File upload misconfigs can lead to RCE
- Check sudo permissions early in privesc