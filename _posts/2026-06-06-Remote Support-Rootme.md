---
title: Remote Support - Rootme
date: 2026-06-06 15:53:00 +0700
categories: [Challenge, Rootme]
tags: [Windows, medium, practice, challenge]
---


>Your system administrator asks you to help him harden his machine using the tools he has installed.You’ve taken a network capture and a memory dump, so show him that he’s still a long way off! The flag is of the form sha256(part1+part2+part3+part4+part5)

The chal provides 2 files: 1 pcap file, 1 memory dump file

Beginning with the pcap file, It captured SMB2 protocol
![All](/assets/18-Remote%20Support/1.png){: .normal }

Ah, author gave me a free gift in a file of Sticky Note. It's part 1 of the flag: 
4lw4ys_G1ve_a_Fr33_G1ft

![All](/assets/18-Remote%20Support/2.png){: .normal }



`vol3 -f memdump.raw -o ./ windows.dumpfiles --virtaddr 0xe00074a41c40`

`dotnet run -- "../Rootme/Remote Support/ch44/pid.2460.dmp"`

`Part4 : I'm_a_F0rensic_L0ver.And.You?`


```
import struct
from PIL import Image

with open("pid.220_2.data", "rb") as f:
    data = f.read()

W, H = 1920, 1024
frame_size = W * H * 4  # BGRA

for i in range(122):
    offset = i * frame_size
    frame_data = data[offset:offset + frame_size]
    img = Image.frombytes("RGBA", (W, H), frame_data, "raw", "BGRA")
    img.save(f"frame_{i:03d}.png")
    print(f"Saved frame {i}")
```

Part3 is R3m0t3_D3skt0p_1s_CO0l_??
