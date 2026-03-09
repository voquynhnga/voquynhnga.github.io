---
title: Whisper- HTB Sherlocks
date: 2026-03-09 21:27:00 +0700
categories: [Forensics, Hackthebox]
tags: [DFIR, window, easy]
---


> The SOC team received an alert on 21st January 2025 about suspicious activity originating from an employee's system (Alpha). Alpha was not authorized to engage in any offensive hacking activities within the network. The Incident Response (IR) team quickly acted, isolating the system so you can perform a thorough triage and investigation to determine the scope of the breach and potential impact on the network.

Challenge provided 2 log files: 2025-01-21T02_02_01_6418515_CopyLog.csv and 2025-01-21T02_02_01_6418515_SkipLog.csv
We have to complete 14 tasks

1. What is the hostname of the company computer involved in the unauthorized activity?
-> Firstly, i just found only 1 "artifact". It's ConsoleHost_history.txt. And it contains a computer's hostname, i was fully convinced that it's correct. But not ~~. Then, i realized that modified date of this file is not 20-21/1. Therefore, i forward my mind to the event, tadaa, i see it. Computer's host name is Helpdesk.
![All](/assets/3/1.png){: .normal }

2. What is the IP address associated with the machine?
-> I found the answer in HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces
![All](/assets/3/2.png){: .normal }

3. What is the Security Identifier (SID) of the computer involved in the incident?
-> This question i used a trick :)) I tried to enter many answer but they all aren't right. So I skip this question. And when come to question 12, i found the answer by filter eventId 4720 in Security event. It is S-1-5-21-953115734-2025219997-3674921352-1002, i only need remove 1002.

4. What was the timezone setting of the machine?
-> HKLM\SYSTEM\ControlSet001\Control\TimeZoneInformation

5. Which tool did the attacker use to escalate privileges and extract sensitive credentials from the system?
-> I use MFT Explorer to watch $MFT file. I found in folder Downloads of user Alpha had mimikatz_trunk.zip. And in prefetch, i saw 2 file prefetch of mimikatz.exe
-> mimikatz.exe

Mimikatz is an open-source post-exploitation tool designed for Windows operating systems that extracts plaintext passwords, hashes, PIN codes, and Kerberos tickets from memory. 
{: .prompt-tip }

6. What is the timestamp of the last execution of the credential extraction tool on the system used by the attacker?
-> I tried entering modified date of file mimikatz prefetch but it's not true. Then, i used PECmd to show detail base on file prefetch. 

![All](/assets/3/3.png){: .normal }



7. What is the timestamp when the attacker first downloaded the credential extraction tool onto the system?

![All](/assets/3/4.png){: .normal }
-> I remember about the file zip.

8. After using the previously mentioned tool, the attacker downloaded a script to scan the domain controller. What is the URL from which the attacker downloaded this script?
-> I used DB Browser (SQLite) to open "C\Users\Alpha\AppData\Local\Microsoft\Edge\User Data\Default\History". 
![All](/assets/3/5.png){: .normal }

9. What is the filename of the script? (Format - Full path of file, starting with drive letter)
-> Also use the resource like in the question 8

10. What is the timestamp indicating when the attacker deleted the script after it was downloaded?

```
MFTEcmd.exe -f "F:\OneDrive\CTF\HTB\Whisper\C\$J" --csv "F:\OneDrive\CTF\HTB\Whisper\C"
```

![All](/assets/3/6.png){: .normal }

11. What is the name of the shared file that the attacker accessed?

![All](/assets/3/7.png){: .normal }

12. To persist, the attacker created a new user account. What is the SID of the newly created account?

-> I explained in question 3.


13. How many times has the newly created user logged in?

```
$UserSID = "S-1-5-21-953115734-2025219997-3674921352-1002"

# Sử dụng -Path để trỏ vào file .evtx cụ thể
Get-WinEvent -Path ".\Security2" -FilterXPath "*[System[(EventID=4624)] and EventData[Data[@Name='TargetUserSid']='$UserSID']]" | Measure-Object | Select-Object -ExpandProperty Count
```

-> Answer is 0



