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

<br><br>
2. What is the IP address associated with the machine?
-> I found the answer in HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces
![All](/assets/3/2.png){: .normal }

<br><br>
3. What is the Security Identifier (SID) of the computer involved in the incident?


