---
title: Suspicious Threat - HTB Challenge
date: 2026-04-19 09:33:00 +0700
categories: [Challenge, Hackthebox]
tags: [Linux, easy, practice]
---


>Our SSH server is showing strange library linking errors, and critical folders seem to be missing despite their confirmed existence. Investigate the anomalies in the library loading process and filesystem. Look for hidden manipulations that could indicate a userland rootkit.

Following the scenario,  I knew that is the Dynamic Linker Hijacking attack. With ls -la command, I couldn't see the rootkit, so I used the below command to find it
` while read -r line; do echo "$line"; done < /etc/ld.so.preload`

` ldd /bin/ls`

I removed .so file to see the hidden file
 `> /etc/ld.so.preload`

I found the flag.txt by this command
`find / -type f -iname "*flag*" 2>/dev/null`

