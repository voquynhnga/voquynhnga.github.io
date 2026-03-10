---
title: Whisper - HTB Sherlocks
date: 2026-03-09 21:27:00 +0700
categories: [Forensics, Hackthebox]
tags: [DFIR, window, easy, practice]
---


> The SOC team received an alert on 21st January 2025 about suspicious activity originating from an employee's system (Alpha). Alpha was not authorized to engage in any offensive hacking activities within the network. The Incident Response (IR) team quickly acted, isolating the system so you can perform a thorough triage and investigation to determine the scope of the breach and potential impact on the network.<br>

Challenge provided 2 log files: `2025-01-21T02_02_01_6418515_CopyLog.csv` and `2025-01-21T02_02_01_6418515_SkipLog.csv`<br>
We have to complete 14 tasks<br>

### **Question 1. What is the hostname of the company computer involved in the unauthorized activity?**
-> Firstly, I found only 1 "artifact". It's `ConsoleHost_history.txt`. And it contains a computer's hostname, I was fully convinced that it's correct. But it was not ~~. Then, I realized that the modification date didn't align with the incident (20-21/1). Therefore, I shifted my focus to the Window Event Logs (Security), tadaa, I identified it. Computer's host name is Helpdesk.

![All](/assets/3/1.png){: .normal }

<br>
### **Question 2. What is the IP address associated with the machine?**
-> I found the answer in `HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces`

![All](/assets/3/2.png){: .normal }
<br>
### **Question 3. What is the Security Identifier (SID) of the computer involved in the incident?**
-> This question I used a trick :)) I tried to enter many answers but they all aren't right. So I skipped this question. And when I got to question 12, I found the answer by filter event ID 4720 in Security event log. It is S-1-5-21-953115734-2025219997-3674921352-1002, I only need to remove 1002.
<br><br>
### **Question 4. What was the timezone setting of the machine?**
-> HKLM\SYSTEM\ControlSet001\Control\TimeZoneInformation
<br><br>
### **Question 5. Which tool did the attacker use to escalate privileges and extract sensitive credentials from the system?**
-> I used MFT Explorer to watch `$MFT` file. I found in the folder Downloads of user Alpha had `mimikatz_trunk.zip`. And in prefetch, I explored 2 prefetch files of `mimikatz.exe`<br>
-> mimikatz.exe
<br>
> Mimikatz is an open-source post-exploitation tool designed for Windows operating systems that extracts plaintext passwords, hashes, PIN codes, and Kerberos tickets from memory. 
{: .prompt-tip }
<br>
### **Question 6. What is the timestamp of the last execution of the credential extraction tool on the system used by the attacker?**
-> I tried entering modified date of mimikatz prefetch file but it's not true. Then, I used PECmd to show detail base on prefetch file. 

![All](/assets/3/3.png){: .normal }
<br>
### **Question 7. What is the timestamp when the attacker first downloaded the credential extraction tool onto the system?**

![All](/assets/3/4.png){: .normal }

-> I recalled the downloaded zip file.
<br><br>
### **Question 8. After using the previously mentioned tool, the attacker downloaded a script to scan the domain controller. What is the URL from which the attacker downloaded this script?**
-> I used DB Browser (SQLite) to open `C\Users\Alpha\AppData\Local\Microsoft\Edge\User Data\Default\History`. 

![All](/assets/3/5.png){: .normal }
<br>
### **Question 9. What is the filename of the script? (Format - Full path of file, starting with drive letter)**
-> I followed the same process as in question 8
<br>
### **Question 10. What is the timestamp indicating when the attacker deleted the script after it was downloaded?**

```powershell
MFTEcmd.exe -f "F:\OneDrive\CTF\HTB\Whisper\C\$J" --csv "F:\OneDrive\CTF\HTB\Whisper\C"
```

![All](/assets/3/6.png){: .normal }
<br>
### **Question 11. What is the name of the shared file that the attacker accessed?**

![All](/assets/3/7.png){: .normal }
<br>
### **Question 12. To persist, the attacker created a new user account. What is the SID of the newly created account?**
-> I explained in question 3.
<br><br>
### **Question 13. How many times has the newly created user logged in?**

```powershell
$UserSID = "S-1-5-21-953115734-2025219997-3674921352-1002"
# Sử dụng -Path để trỏ vào file .evtx cụ thể
Get-WinEvent -Path ".\Security2" -FilterXPath "*[System[(EventID=4624)] and EventData[Data[@Name='TargetUserSid']='$UserSID']]" | Measure-Object | Select-Object -ExpandProperty Count
```

-> The answer is 0
<br>
### **Question 14. What is the password of the newly created user?**
-> I used secretdumps.exe to extract the NTLM hash of newly created user "Admin" 

```powershell
PS F:\OneDrive\CTF> .\secretsdump.exe -sam "F:\OneDrive\CTF\HTB\Whisper\C\Windows\System32\config\SAM" -system "F:\OneDrive\CTF\HTB\Whisper\C\Windows\System32\config\SYSTEM" LOCAL
Impacket v0.9.17 - Copyright 2002-2018 Core Security Technologies

[*] Target system bootKey: 0x92844110d6639aa82bed018e5800b3b1
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:2e89df757ab47fdeae4dab97bad94ec6:::
A7md-NaS3eR:1001:aad3b435b51404eeaad3b435b51404ee:b2028254cc47c60901cae57393e6a547:::
Admin:1002:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
[*] Cleaning up...
```

Then, I decoded Admin's NTLM hash by [Crack Station](https://crackstation.net/)
![All](/assets/3/8.png){: .normal }







