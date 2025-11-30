---
title: An unusual sighting - CTF Try Out.
date: 2025-11-30 18:27:00 +0700
categories: [Forensics, Hackthebox]
tags: [log, linux]
---

> Heheh this is my first post in my blog. I'm currently a fourth-year student, I'm aware that it's too late to begin writing a blog. But better late than never. I decided to write this blog to improve my writing skills in English and learn forensics seriously. Hope that I'll never give up.
{: .prompt-info }

> This write-up didn't use AI.
{: .prompt-warning }

> As the preparations come to an end, and The Fray draws near each day, our newly established team has started work on refactoring the new CMS application for the competition. However, after some time we noticed that a lot of our work mysteriously has been disappearing! We managed to extract the SSH Logs and the Bash History from our dev server in question. The faction that manages to uncover the perpetrator will have a massive bonus come competition!

We have 2 files `bash_history.txt` and `sshd.log`.
When we connect to the address that the description provided, we have to answer correctly to get the flag.

![Banner](/assets/1_1.png){: .normal }

![First question](/assets/1_2.png){: .normal }
_In ssh log, we can see port 2221 and ip 100.107.36.130 at line 3._

![Second question](/assets/1_3.png){: .normal }
_At line 11, the first 'Accepted password'_

![Third question](/assets/1_4.png){: .normal }
_I saw an ip different from others and it accessed as the root role_

![Fourth question](/assets/1_5.png){: .normal }
_I didn't know the Fingerprint's meaning in this context, so i ask gemini. He said that it's the value of SHA256_

![Fifth question](/assets/1_6.png){: .normal }
_With timestamp as above, we notice at line 22 in bash_history.txt_

![Sixth question](/assets/1_7.png){: .normal }
_I compared the timestamp with sshd.log and i found the ans in line 30_
