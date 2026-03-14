---
title: PhishNet - HTB Sherlocks
date: 2026-03-10 09:54:00 +0700
categories: [Forensics, Hackthebox]
tags: [SOC, very easy, practice]
---


> An accounting team receives an urgent payment request from a known vendor. The email appears legitimate but contains a suspicious link and a .zip attachment hiding malware. Your task is to analyze the email headers, and uncover the attacker's scheme.<br>

Challenge provided a .eml files: `email.eml`. In this email, we had 1 zip file of invoice <br>

![All](/assets/4/1.png){: .normal }

Initially, I cannot extract the zip file, so I tried using followed command

```shell
zip -FF Invoice_2025_Payment.zip --out fixed_file.zip
```

Hmmm, it still didn't work. I watch the official writeup a bit and I realized that it didn't need extract the zip file :)) . <br>
We have to answer 11 questions<br>
Most of question were in the .eml file. We just need to used notepad or VSCode to explorer answers

### **Question 9. What is the SHA-256 hash of the attachment?**

```shell
sha256sum Invoice_2025_Payment.zip
8379c41239e9af845b2ab6c27a7509ae8804d7d73e455c800a551b22ba25bb4a  Invoice_2025_Payment.zip
```

### **Question 11. Which MITRE ATT&CK techniques are associated with this attack?**

![All](/assets/4/2.png){: .normal }








