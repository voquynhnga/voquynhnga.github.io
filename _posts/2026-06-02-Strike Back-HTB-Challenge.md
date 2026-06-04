---
title: Strike Back - HTB Challenge
date: 2026-06-02 22:08:00 +0700
categories: [Challenge, Hackthebox]
tags: [Windows, medium, practice, challenge]
---


>A fleet of steam blimps waits the final signal from their commander in order to attack gogglestown kingdom. A recent cyber attack had us thinking if the enemy managed to discover our plans and prepare a counter-attack. Will the fleet get ambused???

Hmm, beginning with an analysis of the pcap file, I noticed that user downloaded a malicious file named freesteam.exe

![All](/assets/15-strike back/1.png){: .normal }

I uploaded this file to virustotal to identify it. And I saw that is CobaltStrike trojan.
![All](/assets/15-strike back/2.1.png){: .normal }

its behavior:
![All](/assets/15-strike back/2.png){: .normal }

I used IDA to reverse freesteam.exe but it didn't have any valuable information.
![All](/assets/15-strike back/3.png){: .normal }

Therefore, I continued with the iVd9 file. It is a shellcode of CobaltStrike. 
![All](/assets/15-strike back/4.png){: .normal }

I found a tool named CobaltStrikeParser and CobaltStrikeScan but they only extracted basic configuration information. 
![All](/assets/15-strike back/5.1.png){: .normal }


After researching CobaltStrike, I knew that I had to find the key to decrypt the data exfiltrated. And the key wasn't in static PE file, it only existed in runtime memory. And I found the tool which can extract key from memory. It is https://github.com/DidierStevens/Beta

`python .\cs-extract-key.py -t a4940d6ff0a59421822467d80d1b620bc7ecfa661c452a85c0486b56aa752e908c4aeb3f2f0a64d9c02d7025713867ee ..\freesteam.dmp`
> The -t flag takes a test value — the raw bytes from the smallest POST body in the pcap (divisible by 16 for AES block size) — which the tool uses to verify the HMAC signature when scanning the memory dump for keys.
{: .prompt-tip }

![All](/assets/15-strike back/5.2.png){: .normal }

I used the following filter and selected File->Export Specified Packets to save these frames into a new pcap file.
![All](/assets/15-strike back/5.3.png){: .normal }

`python .\cs-parse-http-traffic.py -k bf2d35c0e9b64bc46e6d513c1d0f6ffe:3ae7f995a2392c86e3fa8b6fbc3d953a ..\capture1.pcap`

```
    Packet number: 1
    HTTP request POST
    http://192.168.1.9/submit.php?id=1909272864
    Length raw data: 68
    Counter: 2
    Callback: 16 BEACON_GETUID
    b'WS02\\npatrick (admin)'

    Packet number: 2
    HTTP request POST
    http://192.168.1.9/submit.php?id=1909272864
    Length raw data: 724
    Counter: 3
    Callback: 24 BEACON_OUTPUT_NET
    b"Account information for npatrick on \\\\localhost:\n\nUser name                    npatrick\nFull Name                    npatrick\nComment                      Fleet Commander\nUser's Comment               \nCountry code                 0\nAccount active               Yes\nAccount expires              Never\nAccount type                 Admin\n\nPassword last set            221 hours ago\nPassword expires             Yes\nPassword changeable          Yes\nPassword required            Yes\nUser may change password     Yes\n\nWorkstations allowed         \nLogon script                 \nUser profile                 \nHome directory               \nLast logon                   11/19/2021 12:41:23\n"

    Packet number: 3
    HTTP request POST
    http://192.168.1.9/submit.php?id=1909272864
    Length raw data: 548
    Counter: 4
    Callback: 21 BEACON_OUTPUT_HASHES
    b'Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::\nDefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::\nGuest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::\nJohn Doe:1001:aad3b435b51404eeaad3b435b51404ee:37fbc1731f66ad4e524160a732410f9d:::\nnpatrick:1002:aad3b435b51404eeaad3b435b51404ee:3c7c8387d364a9c973dc51a235a1d0c8:::\nWDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:c81c8295ec4bfa3c9b90dcd6c64727e2:::\n'

    Packet number: 5
    HTTP request POST
    http://192.168.1.9/submit.php?id=1909272864
    Length raw data: 4516
    Counter: 5
    Callback: 32 UNKNOWN

    Authentication Id : 0 ; 334782 (00000000:00051bbe)
    Session           : Interactive from 1
    User Name         : npatrick
    Domain            : WS02
    Logon Server      : WS02
    Logon Time        : 11/19/2021 12:40:19 PM
    SID               : S-1-5-21-3301052303-2181805973-2384618940-1002
            msv :
            [00000003] Primary
            * Username : npatrick
            * Domain   : .
            * NTLM     : 3c7c8387d364a9c973dc51a235a1d0c8
            * SHA1     : 44cb46af6b1e8c5873bee400115d1694e650c5b4
            tspkg :
            wdigest :
            * Username : npatrick
            * Domain   : WS02
            * Password : (null)
            kerberos :
            * Username : npatrick
            * Domain   : WS02
            * Password : (null)
            ssp :
            credman :

    Authentication Id : 0 ; 334736 (00000000:00051b90)
    Session           : Interactive from 1
    User Name         : npatrick
    Domain            : WS02
    Logon Server      : WS02
    Logon Time        : 11/19/2021 12:40:19 PM
    SID               : S-1-5-21-3301052303-2181805973-2384618940-1002
            msv :
            [00000003] Primary
            * Username : npatrick
            * Domain   : .
            * NTLM     : 3c7c8387d364a9c973dc51a235a1d0c8
            * SHA1     : 44cb46af6b1e8c5873bee400115d1694e650c5b4
            tspkg :
            wdigest :
            * Username : npatrick
            * Domain   : WS02
            * Password : (null)
            kerberos :
            * Username : npatrick
            * Domain   : WS02
            * Password : (null)
            ssp :
            credman :

    Authentication Id : 0 ; 997 (00000000:000003e5)
    Session           : Service from 0
    User Name         : LOCAL SERVICE
    Domain            : NT AUTHORITY
    Logon Server      : (null)
    Logon Time        : 11/19/2021 12:40:12 PM
    SID               : S-1-5-19
            msv :
            tspkg :
            wdigest :
            * Username : (null)
            * Domain   : (null)
            * Password : (null)
            kerberos :
            * Username : (null)
            * Domain   : (null)
            * Password : (null)
            ssp :
            credman :

    Authentication Id : 0 ; 46420 (00000000:0000b554)
    Session           : Interactive from 1
    User Name         : DWM-1
    Domain            : Window Manager
    Logon Server      : (null)
    Logon Time        : 11/19/2021 12:40:12 PM
    SID               : S-1-5-90-0-1
            msv :
            tspkg :
            wdigest :
            * Username : WS02$
            * Domain   : WORKGROUP
            * Password : (null)
            kerberos :
            ssp :
            credman :

    Authentication Id : 0 ; 46226 (00000000:0000b492)
    Session           : Interactive from 1
    User Name         : DWM-1
    Domain            : Window Manager
    Logon Server      : (null)
    Logon Time        : 11/19/2021 12:40:12 PM
    SID               : S-1-5-90-0-1
            msv :
            tspkg :
            wdigest :
            * Username : WS02$
            * Domain   : WORKGROUP
            * Password : (null)
            kerberos :
            ssp :
            credman :

    Authentication Id : 0 ; 996 (00000000:000003e4)
    Session           : Service from 0
    User Name         : WS02$
    Domain            : WORKGROUP
    Logon Server      : (null)
    Logon Time        : 11/19/2021 12:40:12 PM
    SID               : S-1-5-20
            msv :
            tspkg :
            wdigest :
            * Username : WS02$
            * Domain   : WORKGROUP
            * Password : (null)
            kerberos :
            * Username : ws02$
            * Domain   : WORKGROUP
            * Password : (null)
            ssp :
            credman :

    Authentication Id : 0 ; 26445 (00000000:0000674d)
    Session           : Interactive from 0
    User Name         : UMFD-0
    Domain            : Font Driver Host
    Logon Server      : (null)
    Logon Time        : 11/19/2021 12:40:12 PM
    SID               : S-1-5-96-0-0
            msv :
            tspkg :
            wdigest :
            * Username : WS02$
            * Domain   : WORKGROUP
            * Password : (null)
            kerberos :
            ssp :
            credman :

    Authentication Id : 0 ; 26411 (00000000:0000672b)
    Session           : Interactive from 1
    User Name         : UMFD-1
    Domain            : Font Driver Host
    Logon Server      : (null)
    Logon Time        : 11/19/2021 12:40:12 PM
    SID               : S-1-5-96-0-1
            msv :
            tspkg :
            wdigest :
            * Username : WS02$
            * Domain   : WORKGROUP
            * Password : (null)
            kerberos :
            ssp :
            credman :

    Authentication Id : 0 ; 25289 (00000000:000062c9)
    Session           : UndefinedLogonType from 0
    User Name         : (null)
    Domain            : (null)
    Logon Server      : (null)
    Logon Time        : 11/19/2021 12:40:12 PM
    SID               : 
            msv :
            tspkg :
            wdigest :
            kerberos :
            ssp :
            credman :

    Authentication Id : 0 ; 999 (00000000:000003e7)
    Session           : UndefinedLogonType from 0
    User Name         : WS02$
    Domain            : WORKGROUP
    Logon Server      : (null)
    Logon Time        : 11/19/2021 12:40:12 PM
    SID               : S-1-5-18
            msv :
            tspkg :
            wdigest :
            * Username : WS02$
            * Domain   : WORKGROUP
            * Password : (null)
            kerberos :
            * Username : ws02$
            * Domain   : WORKGROUP
            * Password : (null)
            ssp :
            credman :

    Extra packet data: b'\x00\x00\x00'

    Packet number: 6
    HTTP request POST
    http://192.168.1.9/submit.php?id=1909272864
    Length raw data: 324
    Counter: 6
    Callback: 22 TODO
    b'\xff\xff\xff\xfe'
    ----------------------------------------------------------------------------------------------------
    C:\Users\npatrick\Desktop\*
    D       0       11/19/2021 12:24:08     .
    D       0       11/19/2021 12:24:08     ..
    F       5175    11/11/2021 03:24:13     cheap_spare_parts_for_old_blimps.docx
    F       282     11/10/2021 07:02:24     desktop.ini
    F       24704   11/11/2021 03:22:16     gogglestown_citizens_osint.xlsx
    F       62393   11/19/2021 12:24:10     orders.pdf

    ----------------------------------------------------------------------------------------------------

    Packet number: 12
    HTTP request POST
    http://192.168.1.9/submit.php?id=1909272864
    Length raw data: 62572
    Counter: 7
    Callback: 2 DOWNLOAD_START
    parameter1: 0
    length: 62393
    filenameDownload: C:\Users\npatrick\Desktop\orders.pdf

    Counter: 8
    Callback: 8 DOWNLOAD_WRITE
    Length: 62393
    MD5: 00f542efefccd7a89a55c133180d8581

    Counter: 9
    Callback: 9 DOWNLOAD_COMPLETE
    b'\x00\x00\x00\x00'


    Callbacks summary:
    2 DOWNLOAD_START: 1
    8 DOWNLOAD_WRITE: 1
    9 DOWNLOAD_COMPLETE: 1
    16 BEACON_GETUID: 1
    21 BEACON_OUTPUT_HASHES: 1
    22 TODO: 1
    24 BEACON_OUTPUT_NET: 1
    32 UNKNOWN: 1
```

After decrypting the contents of method POST, we could see the attacker downloaded a file named orders.pdf. I extracted this file by the following command

`python .\cs-parse-http-traffic.py -k bf2d35c0e9b64bc46e6d513c1d0f6ffe:3ae7f995a2392c86e3fa8b6fbc3d953a -e ../capture1.pcap`

The file contains the flag
![All](/assets/15-strike back/5.png){: .normal }

In conclusion, the kill chain for this chal is:
- **Stage 1: Execution & Persistence:**
Use freesteam.exe to call malicious dll (dll sideloading)

- **Stage 2: Defense Evasion:**
Use Memory Obfuscation technique to encrypt the AES key and beacon config in RAM (that is the reason why I tried to use Yara to scan many times but there has no result).
Create \pipe\MSSE-xxxx-server to hide the interval communication (Named Pipe Communication)

- **Stage 3: Credential Access:**
Attacker injected Mimikatz module to run command `logonpasswords` and get the NTLM Hashes of Admin account

- **Stage 4: C2 and Exfiltration:**
GET /match and POST /submit.php are used to communicate with server and exfiltrate data
