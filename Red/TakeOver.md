# TryHackMe - TakeOver Writeup

**Difficulty:** Easy  
**Category:** Subdomain Enumeration / Subdomain Takeover  
**Author:** YourName  
**Date:** 27-02-2026  

---

## Summary
This challenge revolves around subdomain enumeration and subdomain takeover. The goal is to find a hidden subdomain that is vulnerable to takeover and retrieve the flag hidden within its SSL certificate chain.

---

## Reconnaissance

The room description mentions that the company is *"rebuilding their support"* — a strong hint that a `support` subdomain exists.

First, added the target IP to `/etc/hosts`:
```bash
echo "10.113.155.208 futurevera.thm" >> /etc/hosts
```

---

## Step 1 — Support Subdomain Discovery

Based on the hint in the room description, added the support subdomain:
```bash
echo "10.113.155.208 support.futurevera.thm" >> /etc/hosts
```

Visited `https://support.futurevera.thm` in the browser.

---

## Step 2 — SSL Certificate Inspection

On the support subdomain, clicked the **padlock icon** in the browser → **Show Certificate** → checked **Subject Alternative Names (SANs)**.

Found a hidden subdomain listed in the cert:
```
secrethelpdesk934752.support.futurevera.thm
```

This is the subdomain that is vulnerable to takeover.

---

## Step 3 — Accessing the Vulnerable Subdomain

Added the newly found subdomain to `/etc/hosts`:
```bash
echo "10.113.155.208 secrethelpdesk934752.support.futurevera.thm" >> /etc/hosts
```

Visited `https://secrethelpdesk934752.support.futurevera.thm` in the browser.

The site threw an error trying to connect to an **unclaimed AWS S3 bucket**:
```
flag{beea0d6edfcee06a59b83fb50ae81b2f}.s3-website-us-west-3.amazonaws.com
```

This confirms a **subdomain takeover vulnerability** — the subdomain points to an AWS S3 bucket that no longer exists and could be claimed by an attacker.

---

## Flag

```
flag{beea0d6edfcee06a59b83fb50ae81b2f}
```

---

## What is Subdomain Takeover?

The vulnerable subdomain was pointing to an AWS S3 bucket that the company abandoned. An attacker could register that S3 bucket and serve malicious content under the company's trusted domain — this is a **subdomain takeover**.

---

## Lessons Learned
- Read room descriptions carefully — they often contain hints
- Always inspect SSL certificates for hidden subdomains in Subject Alternative Names
- Abandoned cloud service DNS records can lead to subdomain takeover vulnerabilities