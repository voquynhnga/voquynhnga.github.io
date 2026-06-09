---
title: Remote Support - Rootme
date: 2026-06-06 15:53:00 +0700
categories: [Challenge, Rootme]
tags: [Windows, hard, practice, challenge]
---

> Your system administrator asks you to help him harden his machine using the tools he has installed. You've taken a network capture and a memory dump, so show him that he's still a long way off! The flag is of the form `sha256(part1+part2+part3+part4+part5)`

The challenge provides 2 files: 1 pcap file, 1 memory dump file.


## Step 1 — PCAP Analysis (Part1)

Opening the `.pcap` file in Wireshark, the traffic is primarily **SMB2** protocol.

![All](/assets/18-Remote%20Support/1.png){: .normal }

I exported all SMB objects via `File → Export Objects → SMB`:
![All](/assets/18-Remote%20Support/1.2.png){: .normal }

The file `StickyNotes.snt` is a Windows Sticky Notes database. Opening it reveals a note containing Part1 of the flag.

![All](/assets/18-Remote%20Support/2.png){: .normal }

> **Part1: `4lw4ys_G1ve_a_Fr33_G1ft`**

## Step 2 — KeePass Memory Dump (Part4)

Firstly,I dumped the KeePass database file that is currently open in memory:

![All](/assets/18-Remote%20Support/5.png){: .normal }

Then used the **keepass-password-dumper** tool to extract the master password directly from process memory. 

```bash
dotnet run -- "pid.2460.dmp"
```
![All](/assets/18-Remote%20Support/6.png){: .normal }

> **Part4: `I'm_a_F0rensic_L0ver.And.You?`**

## Step 3 — RDP Frames from mstsc.exe (Part3)

> The `mstsc.exe` process (Microsoft Remote Desktop Client) stores the RDP video frame buffer in memory. 
{.prompt-tip}
Dump the memory map of the process:

```bash
vol3 -f memdump.raw windows.memmap.Memmap --pid 220 --dump
```
The resulting file `pid.220_2.data` contains raw BGRA frame data. Extract it into individual PNG frames:

```python
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

![All](/assets/18-Remote%20Support/9.png){: .normal }

> **Part3: `R3m0t3_D3skt0p_1s_CO0l_??`**


## Step 4 — TeamViewer Registry Crypto (Part2)

The file `hive_hklm.reg` is encoded as **UTF-16LE**. Convert it to UTF-8 first:

```bash
iconv -f UTF-16LE -t UTF-8 hive_hklm.reg > hklm_utf8.txt
grep -i "SecurityPasswordAES" hklm_utf8.txt
```
![All](/assets/18-Remote%20Support/10.png){: .normal }

> **TeamViewer version 7** uses AES-128 CBC encryption with a **hardcoded key and IV** — a well-known vulnerability. 
{.prompt-tip}

```python
from Crypto.Cipher import AES

key = bytes([0x06,0x02,0x00,0x00,0x00,0xa4,0x00,0x00,
             0x52,0x53,0x41,0x31,0x00,0x04,0x00,0x00])
iv  = bytes([0x01,0x00,0x01,0x00,0x67,0x24,0x4F,0x43,
             0x6E,0x67,0x50,0x3D,0x28,0x74,0x1E,0x01])

encrypted = bytes([
    0x2f,0x6e,0xa5,0xa2,0xcd,0xb6,0xc5,0x22,0x2d,0x1b,0xfe,0x1f,0x1f,0xac,0x75,0x22,0x37,
    0xe0,0x52,0xbe,0x77,0xde,0x95,0x42,0xd1,0x9c,0xdc,0x90,0x09,0xe2,0x8f,0x3d,0x44,0xc4,
    0xd6,0xcc,0x79,0xa3,0x11,0x2d,0xaf,0x58,0xd4,0x2a,0x16,0xd0,0x9a,0xdf,0x6f,0x0e,0x94,
    0xa4,0x89,0xad,0x8d,0x61,0x6c,0xff,0x42,0xae,0xc5,0x31,0x22,0x1a,0xb2,0x1c,0xcc,0x68,
    0x66,0xb4,0x84,0x1a,0x7d,0x08,0x44,0x85,0x71,0xc3,0x63,0x06,0xef,0xb5,0xb3,0xbd,0x0e,
    0x09,0xff,0x6f,0xa4,0x99,0xfb,0x23,0xa3,0x09,0xe2,0xd5
])

cipher = AES.new(key, AES.MODE_CBC, iv)
decrypted = cipher.decrypt(encrypted)
print(decrypted.decode('utf-16-le', errors='ignore').rstrip('\x00'))
```
![All](/assets/18-Remote%20Support/11.png){: .normal }
> **Part2: `V3ry-D1fficULt.b3c@us3.in.R3gistrY`**


## Step 5 — DPAPI Decryption from save_rdp.rdg (Part5)

The file `save_rdp.rdg` is an **RDG Manager** XML file containing saved RDP credentials:
![All](/assets/18-Remote%20Support/13.png){: .normal }

The password blob is encrypted with **Windows DPAPI**. Decrypting it requires the user's DPAPI masterkey, which in turn requires the user's password.

### Step 5.1 — Locate the DPAPI masterkey in memory

```bash
vol3 -f memdump.raw windows.filescan.FileScan | grep -i "Protect"
```
![All](/assets/18-Remote%20Support/12.png){: .normal }

The masterkey file could not be dumped directly via `dumpfiles`. Instead, search for it in raw memory using the GUID stored as UTF-16LE:

```python
import struct, re

guid_utf16 = "e156f1f5-3b83-4716-b14c-c0d956849246".encode('utf-16-le')

with open("memdump.raw", "rb") as f:
    data = f.read()

matches = [m.start() for m in re.finditer(re.escape(guid_utf16), data)]

for offset in matches:
    for back in range(0, 150, 4):
        header_start = offset - back
        version = struct.unpack_from("<I", data, header_start)[0]
        if version == 2:
            chunk = data[header_start:header_start + 1000]
            with open(f"mk_v2_{hex(header_start)}.bin", "wb") as out:
                out.write(chunk)
            print(f"Saved mk_v2_{hex(header_start)}.bin")
            break
```

This produces candidate masterkey files including `mk_v2_0x71109c8.bin`.

### Step 5.2 — Crack the user password

Use **DPAPImk2john** to generate a crackable hash from the masterkey file:
```bash
python3 DPAPImk2john.py -S S-1-5-21-2145360380-4246029103-3466432845-1001 -mk mk_v2_0x71109c8.bin -c local
```

![All](/assets/18-Remote%20Support/14.png){: .normal }


Then crack with hashcat using **mode 15900** (DPAPI masterkey v2):
```bash
hashcat -m 15900 hashcat -m 15900 dpapi.hash ~/ctf/rockyou.txt
```

![All](/assets/18-Remote%20Support/15.png){: .normal }

Result: **`forensic1`**

### Step 6.3 — Decrypt the masterkey and DPAPI blob

With the password in hand, derive the masterkey and decrypt the DPAPI blob using **impacket**:

```python
from impacket.dpapi import MasterKeyFile, MasterKey, DPAPI_BLOB, deriveKeysFromUser
import base64

SID      = "S-1-5-21-2145360380-4246029103-3466432845-1001"
password = "forensic1"

with open("mk_v2_0x71109c8.bin", "rb") as f:
    mkdata = f.read()

mkf = MasterKeyFile(mkdata)
# MasterKeyFile header is 128 bytes
HEADER_SIZE = 4 + 4 + 4 + 72 + 4 + 4 + 4 + 8 + 8 + 8 + 8
mk = MasterKey(mkdata[HEADER_SIZE:HEADER_SIZE + mkf['MasterKeyLen']])

# Derive decryption key from password + SID
keys = deriveKeysFromUser(SID, password)
decrypted_mk = None
for key in keys:
    result = mk.decrypt(key)
    if result:
        decrypted_mk = result
        break

# Decrypt the DPAPI blob from save_rdp.rdg
blob_b64 = (
    "AQAAANCMnd8BFdERjHoAwE/Cl+sBAAAA9fFW4YM7FkexTMDZVoSSRgAAAAACAAAAAAAQZgAAAAEAACAA"
    "AACGikb/Wn5Fs9X6Ia6sW9SbJtkAhl1QRXi/vKnrnQ90bAAAAAAOgAAAAAIAACAAAACMjvY1CihQ7o"
    "iw4Lbf0RjBlEdBD/ulPrlAGiEUGycjD0AAAAA70WN+m2kMOcdoZ2/aPHLS9D4VvbDC8U56BE2CtZW"
    "GnbbRc0VnYElm1zKQxJ4FYCRshGarVdOgLX3TDKKE1eV2QAAAAO02VD2Nx7El4EE1qKBklgFuLCES"
    "c9bhp6I6r7BVfvX8AwcOoMvy3Gzf6rdgpCSE1dcJmNSvFNGhiA3WoAmpcEI="
)
blob = DPAPI_BLOB(base64.b64decode(blob_b64))
plaintext = blob.decrypt(decrypted_mk)
print(plaintext.decode('utf-16-le').rstrip('\x00'))
```
![All](/assets/18-Remote%20Support/16.png){: .normal }

> **Part5: `N3v3r_G0nn@-G1v3.You.Up`**

Flag is : `bda1b8de3a513d85d86581d5a142071068dedd973c1796a122e33c16cc32af30`
