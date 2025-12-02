---
title: Silicon Data Sleuthing - CTF Try Out
date: 2025-11-30 18:27:00 +0700
categories: [Forensics, Hackthebox]
tags: [firmware, linux, easy]
---


> In the dust and sand surrounding the vault, you unearth a rusty PCB... You try to read the etched print, it says Open..W...RT, a router! You hand it over to the hardware gurus and to their surprise the ROM Chip is intact! They manage to read the data off the tarnished silicon and they give you back a firmware image. It's now your job to examine the firmware and maybe recover some useful information that will be important for unlocking and bypassing some of the vault's countermeasures!

Challenge provided 1 file chal_router_dump.bin
When we connect to the address that the description provided, we have to answer 7 questions correctly to get the flag.

![All](/assets/2/1.png){: .normal }

-> For most question, i've just used strings grep to find answer. 
But at ques 3, i lost a lot of time to ask Gemini :))  And after long time, i realized it had jffs2, so i install extension jefferson and binwalk to extract. 
