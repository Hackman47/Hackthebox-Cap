htb-cap-writeup

🔍 Overview

Cap is an easy Linux machine focused on network traffic analysis, credential extraction, and privilege escalation through misconfigured Linux capabilities.

⸻

🎯 Objectives

* Analyse captured network traffic
* Extract credentials from insecure protocols
* Gain initial system access
* Perform privilege escalation

⸻

🌐 Enumeration

Initial reconnaissance identified the following open services:

* FTP (21)
* SSH (22)
* HTTP (80)

The web application exposed downloadable PCAP files, indicating potential access to captured network traffic.

⸻

📡 PCAP Analysis

The PCAP file was analysed using Wireshark.
By inspecting TCP streams, plaintext FTP credentials were identified:

USER nathan  
PASS <redacted>

This highlights the insecurity of FTP, where credentials are transmitted without encryption.

⸻

🔑 Initial Access

The recovered credentials were reused to authenticate via SSH:

```
ssh nathan@10.10.X.X
```
Successful login confirmed credential reuse across services.

⸻

⚙️ Privilege Escalation

System enumeration was performed to identify potential escalation vectors:

```
getcap -r / 2>/dev/null
```

This revealed:
/usr/bin/python3.8 = cap_setuid+ep

The cap_setuid capability allows the binary to change its effective user ID, which can be abused to gain elevated privileges.

🚨 Exploitation

Python was used to escalate privileges by setting the UID to root:

```
/usr/bin/python3.8 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

Verification:

id
uid=0(root)

🏁 Root Access

Root access was successfully obtained.

⸻

💡 Key Learnings

* FTP transmits credentials in plaintext and should not be used in secure environments
* Credential reuse significantly increases risk across services
* Linux capabilities can introduce privilege escalation paths if misconfigured
* Enumeration tools such as getcap are critical in post-exploitation

⸻

🧠 Conclusion

This machine demonstrates a full attack chain from network traffic analysis to root compromise. It reinforces the importance of secure protocols, proper credential management, and thorough system enumeration.
