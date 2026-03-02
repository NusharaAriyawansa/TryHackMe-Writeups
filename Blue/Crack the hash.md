# TryHackMe - Crack the Hash Writeup

**Difficulty:** Easy  
**Category:** Hash Cracking  
**Date:** 03-03-2026  

---

## Summary
This challenge revolves around identifying and cracking various types of hashes. Level 1 uses common hashes crackable with online tools, while Level 2 introduces salted hashes and less common hash types requiring hashcat.

---

## Tools Used
- CrackStation (https://crackstation.net)
- hashcat
- rockyou.txt wordlist

---

## Level 1

### Q1 — `48bb6e862e54f2a795ffc4e541caed4d`

Pasted into CrackStation:
```
Type: MD5
Result: easy
```

### Q2 — `CBFDAC6008F9CAB4083784CBD1874F76618D2A97`

Pasted into CrackStation:
```
Type: SHA1
Result: password123
```

### Q3 — `1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032`

Pasted into CrackStation:
```
Type: SHA256
Result: letmein
```

### Q4 — `$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom`

CrackStation could not crack this — it is **bcrypt**. Since the hint suggested a 4 character password, filtered rockyou.txt first:
```bash
grep -E '^.{4}$' /usr/share/wordlists/rockyou.txt > 4char.txt
```

Then cracked with hashcat:
```bash
hashcat -a 0 -m 3200 hash.txt 4char.txt --force
```

```
Type: bcrypt
Result: bleh
```

### Q5 — `279412f945939ba78ce0758d3fd83daa`

Pasted into CrackStation:
```
Type: MD4
Result: Eternity22
```

---

## Level 2

### Q1 — `F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85`

CrackStation failed. Used hashcat with SHA256 mode:
```bash
hashcat -a 0 -m 1400 hash.txt /usr/share/wordlists/rockyou.txt --force
```

```
Type: SHA256
Result: paule
```

### Q2 — `1DFECA0C002AE40B8619ECF94819CC1B`

Hash identifier incorrectly identified this as MD5. Both MD5 and MD4 attempts failed:
```bash
hashcat -a 0 -m 0 hash.txt /usr/share/wordlists/rockyou.txt --force   # MD5 - failed
hashcat -a 0 -m 900 hash.txt /usr/share/wordlists/rockyou.txt --force  # MD4 - failed
```

After research, identified as **NTLM**:
```bash
hashcat -a 0 -m 1000 hash.txt /usr/share/wordlists/rockyou.txt --force
```

```
Type: NTLM
Result: n63umy8lkf4i
```

### Q3 — `$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.`
**Salt:** `aReallyHardSalt`

The `$6$` prefix indicates **SHA512crypt**. Filtered wordlist to 6 character passwords and used mode 1800:
```bash
grep -E '^.{6}$' /usr/share/wordlists/rockyou.txt > 6char.txt
hashcat -a 0 -m 1800 hash.txt 6char.txt --force
```

```
Type: SHA512crypt
Result: waka99
```

### Q4 — `e5d8870e5bdd26602cab8dbe07a942c8669e56d6`
**Salt:** `tryhackme`

Salted SHA1 requires the hash file to be formatted as `hash:salt`:
```bash
echo "e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme" > hash.txt
hashcat -a 0 -m 160 hash.txt /usr/share/wordlists/rockyou.txt --force
```

```
Type: HMAC-SHA1
Result: 481616481616
```

---

## Hashcat Mode Reference

| Mode | Hash Type |
|------|-----------|
| 0 | MD5 |
| 900 | MD4 |
| 1000 | NTLM |
| 1400 | SHA256 |
| 1800 | SHA512crypt |
| 3200 | bcrypt |
| 160 | HMAC-SHA1 (with salt) |

---

## Lessons Learned
- Online tools like CrackStation work well for common unsalted hashes
- Hash identifiers can be wrong — always try multiple hash types if one fails
- Filtering wordlists by password length massively speeds up slow algorithms like bcrypt
- Salted hashes require the salt to be appended to the hash in the format `hash:salt`
- The `$6$` prefix means SHA512crypt and `$2y$` means bcrypt