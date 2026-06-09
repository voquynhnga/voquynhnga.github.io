---
title: Memeory - HCMUS CTF 2026
date: 2026-06-09 08:46:00 +0700
categories: [Challenge, HCMUSCTF]
tags: [Windows, CTF, 2026]
---

> When was the last time you touched Volatility? 4-part flag

This was the last forensics challenge that I couldn't solve during the CTF :( To free up my storage, I decided to finish this challenge over two weeks after the contest ended.

The challenge only provided a raw memory image. Since the description explicitly mentioned Volatility, I used familiar Volatility 3 plugins like `pslist`, `pstree`, `filescan`, etc,..

With the `pslist` plugin, we had:
![All](/assets/19-memeory/4.png){: .w75 } 

![All](/assets/19-memeory/1.png){: .w75 } 

Part 1 of the flag was straightforward. I just used `strings` + `grep HCMUSCTF{`, or the `filescan` plugin in Volatility 3 + `grep flag`. Both methods worked fine.
![All](/assets/19-memeory/2.png){: .w75 } 

Part 2 was a trap. We found a `flag2.png` file using `strings` + `grep`.
![All](/assets/19-memeory/3.png){: .w75 } 

During the contest, I initially thought it would involve steganography. But noooooo :( , Using the `pslist` plugin, I noticed the `mspaint.exe` process. I dumped its memory, changed the `.dmp` extension to `.data`, and opened it with GIMP, experimenting with the width and height to render the image properly. Tadaa, I got it! After flipping and rotating the image, I found part 2: `ust_doing_rando`.
![All](/assets/19-memeory/5.png){: .w75 } 

For part 3, I considered RDP bitmap caching, but the `filescan` plugin didn't reveal anything related to RDP. Seeing the `mstsc.exe` process, I tried the same method used in part 2, but it didn't work out of the box. With the help of an AI assistant, I used the following Python script to extract the raw frames:

```python
from PIL import Image
import os

with open("pid.6136.data", "rb") as f:
    data = f.read()

W, H = 1640, 940
frame_size = W * H * 4

num_frames = len(data) // frame_size

print(f"Found {num_frames} full frames")

for i in range(num_frames):
    offset = i * frame_size
    frame_data = data[offset:offset + frame_size]

    img = Image.frombytes(
        "RGBA",
        (W, H),
        frame_data,
        "raw",
        "BGRA"
    )

    img.save(f"frame_{i:03d}.png")

```

![All](/assets/19-memeory/6.png){: .w75 }

After initially struggling with incorrect dimensions (which resulted in skewed images), realizing that the raw dump contained a 480-pixel wide window thumbnail finally gave a clear image.

Part 4 was based on CVE-2023-32784. I used this tool: `https://github.com/vdohney/keepass-password-dumper`

```bash
(base) ┌──(qnv㉿quynhngavo)-[~/ctf/keepass-password-dumper]
└─$ dotnet run -- pid.2964.dmp

```

Combining the output with the context, I identified the 1st and 2nd letters as `a` and `w`. So the master password for the `darkest_secrets.kdbx` file was: `aww_geez_i_sure_hope_nobody_find_this_super_secret_password`
![All](/assets/19-memeory/8.png){: .w75 }
![All](/assets/19-memeory/7.png){: .w75 }


After unlocking the KeePass file, part 4 was: `d_call_it_a_challenge_to_meet_kpi}`

The final flag is: `HCMUS-CTF{d0nt_m1nd_me_just_doing_random_stuff_on_window_4nd_call_it_a_challenge_to_meet_kpi}`

