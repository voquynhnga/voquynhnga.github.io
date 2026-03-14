---
title: Packet Puzzle - HTB Sherlocks
date: 2026-03-14 09:09:00 +0700
categories: [Forensics, Hackthebox]
tags: [SOC, easy, practice]
---


> You are a junior security analyst at a small Japanese cryptocurrency trading company. After detecting suspicious activity on the internal network, you exported a PCAP for further investigation. Analyze this capture to determine whether the environment was compromised and reconstruct the attacker’s actions.<br>

Challenge provided a .eml files: `email.eml`. In this email, we had 1 zip file of invoice <br>

![All](/assets/4/1.png){: .normal }

Initially, I cannot extract the zip file, so I tried using followed command

```shell
zip -FF Invoice_2025_Payment.zip --out fixed_file.zip
```

Hmmm, it still didn't work. I watch the official writeup a bit and I realized that it didn't need extract the zip file :)) . <br>
We have to answer 11 questions<br>
Most of question were in the .eml file. We just need to used notepad or VSCode to explorer answers

### **Question 1. What is the source IP address of the attacker involved in this Attack?**
![All](/assets/6/1.png){: .normal }
-> 192.168.170.128

### **Question 2.How many open ports did the attacker discover on the victim's system?**

```shell
tshark -r NetworkTraffic.pcap -Y "ip.src == 192.168.170.130 && tcp.flags.syn == 1 && tcp.flags.ack == 1" -T fields -e tcp.srcport | sort -n -u
```
![All](/assets/6/3.png){: .normal }
### **Question 3. What is the first open port that responded on the victim's system during reconnaissance?**
![All](/assets/6/3.png){: .normal }
### **Question 4. What is the CVE identifier for the vulnerability exploited by the attacker?**

-> Gemini suggest me that this attack base on the PHP CGI Argument Injection exploition, so I research and identified the answer.
![All](/assets/6/4.png){: .normal }

### **Question 5. What is the name and version of the vulnerable product exploited to get RCE?**
![All](/assets/6/5.png){: .normal }
### **Question 6. What is the username of the victim account?**
![All](/assets/6/6.png){: .normal }
### **Question 7. At what timestamp did the attacker execute the command to gain their initial foothold on the victim system?**
![All](/assets/6/7.png){: .normal }
### **Question 8. What is the MITRE ATT&CK technique ID used by the attacker to gain an initial foothold?**
![All](/assets/6/8.png){: .normal }
### **Question 9. What is the name of the malicious executable the attacker downloaded and executed in memory to facilitate privilege escalation on the endpoint?**
![All](/assets/6/9.png){: .normal }
### **Question 10. What is the command line used by the attacker while performing privilege escalation?**
![All](/assets/6/9.png){: .normal }
### **Question 11.The attacker failed to escalate privileges and was given an error. What is the error?**
![All](/assets/6/11.png){: .normal }




