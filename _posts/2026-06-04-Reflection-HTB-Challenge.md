---
title: Reflection - HTB Challenge
date: 2026-06-04 09:42:00 +0700
categories: [Challenge, Hackthebox]
tags: [Windows, medium, practice, challenge]
---


>You and Miyuki have succeeded in dis-empowering Draeger's army in every possible way. Stopped their fuel-supply plan, arrested their ransomware gang, prevented massive phishing campaigns and understood their tactics and techniques in depth. Now it is the time for the final blow. The final preparations are completed. Everyone is in their stations waiting for the signal. This mission can only be successful if you use the element of surprise. Thus, the signal must remain a secret until the end of the operation. During some last-minute checks you notice some weird behaviour in Miyuki's PC. You must find out if someone managed to gain access to her PC before it's too late. If so, the signal must change. Time is limited and there is no room for errors.

The chal provides only 1 memory dumped file.

I started with command vol3 `vol3 -f memory.raw windows.cmdline.CmdLine` and noticed notepad.exe was run before powershell.exe, which looked so suspicious.
![All](/assets/16-reflection/1.png){: .normal }

Continuing with `vol3 -f memory.raw windows.malfind.Malfind`, I observed two suspicious memory areas injected into the `notepad.exe` process.  
![All](/assets/16-reflection/2.png){: .normal }

So I dumped these memory regions and inspected the output files in notepad++, and saw the DOS header `!This program cannot be run in DOS mode.`, indicating a hidden PE file residing in memory
![All](/assets/16-reflection/3.png){: .normal }
![All](/assets/16-reflection/4.1.png){: .normal }

Therefore, I change the extension of pid.3244.vad.0xb0000-0xb3fff.dmp and reverse it by IDA. 
![All](/assets/16-reflection/4.png){: .normal }

I noticed the truncated command, `powershell-ep bypass -enc ZQBjAGgAbwAgAEgAVABCAHsAZABsAGwAcwBfAGMANABuAF8AYgAzAF8AaAA0AHIAZABfAHQAMABfAGYAMQBuAGQAfQA=`. Decoding this Base64 string, we had the flag
![All](/assets/16-reflection/5.png){: .normal }

In conclusion, the kill chain is:
- The victim downloaded and executed a fake update script (`update.ps1`).
![All](/assets/16-reflection/6.png){: .normal }
- This script contained a malicious command that automatically downloaded a payload (`winmgr.dll`) and injected it directly into memory.










