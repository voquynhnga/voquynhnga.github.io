---
title: Just Some Pages - HTB Sherlocks
date: 2026-03-14 09:09:00 +0700
categories: [Forensics, Hackthebox]
tags: [SOC, medium, practice]
---


> As part of an internal security exercise, a Red Team conducted an attack simulation using JSP web shells and Java-based techniques to bypass traditional defenses. You have been assigned to the Blue Team to analyze the resulting artifacts, reconstruct the attack chain, and design reliable detection strategies to identify similar threats in the future.<br>

Challenge provided 3 pcap files: `DC.pcap` `Web.pcap` `Win-10.pcapng` and 2 Windows Envent Log `DC.evtx` vs `Win-10.etvx`<br>

### **Question 1. Which vulnerability did the attacker leverage to achieve initial compromise?**
-> Initially, I didn't know explorer what to answer this question. Therefore, I continued with the question 2, after knowing of HTTP parameter, Gemini let me know the CVE of this initial compromise

![All](/assets/7/1.png){: .normal }

### **Question 2. Identify the HTTP request parameter abused by the attacker to deliver the encrypted exploit payload.**
```shell
tshark -r Web.pcap -Y "http.request" -T fields -e http.request.uri.query -e urlencoded-form.key | tr '&' '\n' | tr '\t' '\n' | cut -d'=' -f1 | sort -u | grep -v '^$'
```
![All](/assets/7/2.1.png){: .normal }
![All](/assets/7/2.2.png){: .normal }

### **Question 3. Which handler in the web application is vulnerable and gets invoked by the exploit?**
-> I asked Gemini and depended on CVE

### **Question 4. What byte-length threshold should a Base64-encoded payload surpass for the request to be filtered and flagged for this vulnerability?**
-> I asked Gemini and depended on CVE

### **Question 5. Following the initial compromise, which TCP port was used by the attacker to establish an authenticated session through which data was streamed?**

> TDS protocol 
{: .prompt-tip }
![All](/assets/7/5.png){: .normal }

### **Question 6. Using a feature exposed by the application running on that port, which command did the attacker execute as part of their discovery tactic?**
![All](/assets/7/6.png){: .normal }

### **Question 7.What Windows process should defenders monitor to capture suspicious activity of this nature?**
-> I asked Gemini and depended on CVE
### **Question 8. Following execution of the discovery command, what is the process ID (PID) of the web application service running on the victim’s machine?**

```shell
chainsaw search "whoami"
```
![All](/assets/7/8.png){: .normal }

### **Question 9. Which uploaded file allowed the attacker to plant multiple web shells and escalate privileges?**
![All](/assets/7/9.png){: .normal }

### **Question 10. What file was accessed by the attacker to retrieve the administrator’s credentials?**
![All](/assets/7/10.png){: .normal }

### **Question 11. Which PowerShell script (filename) did the attacker download to perform lateral movement?**
![All](/assets/7/11.png){: .normal }

### **Question 12. Which child process spawned by the previously identified web application process would indicate suspicious activity in this scenario?**

-> With the chainsaw command that I indicated in question 8, I could answer this question

```shell
Event_attributes:
  xmlns: http://schemas.microsoft.com/win/2004/08/events/event
Event:
  System:
    Provider_attributes:
      Name: Microsoft-Windows-Sysmon
      Guid: 5770385F-C22A-43E0-BF4C-06F5698FFBD9
    EventID: 1
    Version: 5
    Level: 4
    Task: 1
    Opcode: 0
    Keywords: '0x8000000000000000'
    TimeCreated_attributes:
      SystemTime: 2025-11-03T13:23:19.335743Z
    EventRecordID: 17523
    Correlation: null
    Execution_attributes:
      ProcessID: 2060
      ThreadID: 1348
    Channel: Microsoft-Windows-Sysmon/Operational
    Computer: DESKTOP-2P5FS1A.gotham.city
    Security_attributes:
      UserID: S-1-5-18
  EventData:
    RuleName: '-'
    UtcTime: 2025-11-03 13:23:19.335
    ProcessGuid: D8A809E5-ACC7-6908-E703-000000000A00
    ProcessId: 5320
    Image: C:\Windows\SysWOW64\cmd.exe
    FileVersion: 10.0.10240.16384 (th1.150709-1700)
    Description: Windows Command Processor
    Product: Microsoft® Windows® Operating System
    Company: Microsoft Corporation
    OriginalFileName: Cmd.Exe
    CommandLine: cmd.exe /C whoami
    CurrentDirectory: C:\Program Files (x86)\Apache Software Foundation\Tomcat 6.0\
    User: NT AUTHORITY\SYSTEM
    LogonGuid: D8A809E5-9EF9-6908-E703-000000000000
    LogonId: '0x3e7'
    TerminalSessionId: 0
    IntegrityLevel: System
    Hashes: MD5=42FEFCA6AB3EDFB9AEE8B87292E47DFE,SHA256=6073F3616B310E8EDBFD09744E96F94A7F25FE33C79FF06A0A96E3C8888EB6F2,IMPHASH=7764C33DDF635E8636D8F4B6E7D2C48A
    ParentProcessGuid: D8A809E5-A002-6908-E000-000000000A00
    ParentProcessId: 1472
    ParentImage: C:\Program Files (x86)\Apache Software Foundation\Tomcat 6.0\bin\Tomcat6.exe
    ParentCommandLine: '"C:\Program Files (x86)\Apache Software Foundation\Tomcat 6.0\bin\Tomcat6.exe" //RS//Tomcat6'
    ParentUser: NT AUTHORITY\SYSTEM
```

### **Question 13. On which port was the PowerShell backdoor accepting connections?**
![All](/assets/7/13.png){: .normal }

### **Question 14. Specify the complete destination path the attacker used when copying the sensitive files.**
![All](/assets/7/14.png){: .normal }

### **Question 15. What file was uploaded by the attacker to ensure persistent access?**
![All](/assets/7/14.png){: .normal }

### **Question 16. Which method call should be monitored so defenders can detect the JSP command-execution web shell deployed by an attacker in a WAR file?**
![All](/assets/7/16.png){: .normal }

### **Question 17. Presence of which Java class is a good indicator to monitor for HTTP-tunneler web shells?**
![All](/assets/7/17.png){: .normal }

### **Question 18. Which configuration flag, if present, would be strong indicators of a JSP file browser web shell similar to the attacker’s?**
![All](/assets/7/18.png){: .normal }

### **Question 19. Which Java class writes raw byte content to a file on disk in the attacker’s file-uploader web shell?**
![All](/assets/7/19.png){: .normal }







