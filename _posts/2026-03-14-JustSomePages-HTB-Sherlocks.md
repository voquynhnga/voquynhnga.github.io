---
title: Packet Puzzle - HTB Sherlocks
date: 2026-03-14 09:09:00 +0700
categories: [Forensics, Hackthebox]
tags: [SOC, easy, practice]
---


> As part of an internal security exercise, a Red Team conducted an attack simulation using JSP web shells and Java-based techniques to bypass traditional defenses. You have been assigned to the Blue Team to analyze the resulting artifacts, reconstruct the attack chain, and design reliable detection strategies to identify similar threats in the future.<br>

Challenge provided a .eml files: `email.eml`. In this email, we had 1 zip file of invoice <br>

Q2

```shell
tshark -r Web.pcap -Y "http.request" -T fields -e http.request.uri.query -e urlencoded-form.key | tr '&' '\n' | tr '\t' '\n' | cut -d'=' -f1 | sort -u | grep -v '^$'
```


Q8

```shell
chainsaw search "whoami"
```

Q9 
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





