---
title: Baby - HTB Machine
date: 2026-04-09 22:47:00 +0700
categories: [Machine, Hackthebox]
tags: [Windows, easy, practice]
---


> Hmmm because of midterm examining and working, I couldn't write the write up for 20 days past. Hic. But I still take part in several CTF challenges hehe :)) I'll try to learn and write write up more. And this time, I try the new field that is Machine, not Sherlocks.

>Baby is an easy difficulty Windows machine that features LDAP enumeration, password spraying and exposed credentials. For privilege escalation, the SeBackupPrivilege is exploited to extract registry hives and the NTDS.dit file. A Pass-the-Hash attack can be performed using the uncovered domain hashes ultimately achieving Administrator access.

You will be given network traffic logs from one of the impacted machines. Your task is to conduct a thorough investigation to unravel the attacker's Techniques, Tactics, and Procedures (TTPs).<br>


### **Task 1. What is the fully qualified domain name on Baby?**
![All](/assets/12-baby/1.png){: .normal }
-> I used the below command to get full domain name
`nmap -sC -sV 10.129.234.71`


### **Task 2.Which user has an exposed password in their LDAP description field?**
-> We saw the LDAP protocol was opened in above picture. Hmm let explore what meaning of LDAP.

> Lightweight directory access protocol (LDAP) is a protocol that makes it possible for applications to query user information rapidly.Someone within your office wants to do two things: Send an email to a recent hire and print a copy of that conversation on a new printer. LDAP (lightweight directory access protocol) makes both of those steps possible.
{: .prompt-tip }


Seeing this picture showing 8 users. 
`./windapsearch.py --dc-ip 10.129.234.71 -u "" -d baby.vl --users`
![All](/assets/12-baby/2.1.png){: .normal }

`./windapsearch.py --dc-ip 10.129.234.71 -u "" -d baby.vl --users --full`
![All](/assets/12-baby/2.2.png){: .normal }

-> Tadaa, I saw it.

### **Task 3. Which user account must have its expired password reset before logging in?**
![All](/assets/12-baby/3.1.png){: .normal }

We had 2 new account than above image. That is Ian Walker and Caroline Robinson.

![All](/assets/12-baby/3.2.png){: .normal }




```shell
 ldapsearch -x -b "dc=baby, dc=vl" "*" -H ldap://10.129.234.71 | grep dn
dn: DC=baby,DC=vl
dn: CN=Administrator,CN=Users,DC=baby,DC=vl
dn: CN=Guest,CN=Users,DC=baby,DC=vl
dn: CN=krbtgt,CN=Users,DC=baby,DC=vl
dn: CN=Domain Computers,CN=Users,DC=baby,DC=vl
dn: CN=Domain Controllers,CN=Users,DC=baby,DC=vl
dn: CN=Schema Admins,CN=Users,DC=baby,DC=vl
dn: CN=Enterprise Admins,CN=Users,DC=baby,DC=vl
dn: CN=Cert Publishers,CN=Users,DC=baby,DC=vl
dn: CN=Domain Admins,CN=Users,DC=baby,DC=vl
dn: CN=Domain Users,CN=Users,DC=baby,DC=vl
dn: CN=Domain Guests,CN=Users,DC=baby,DC=vl
dn: CN=Group Policy Creator Owners,CN=Users,DC=baby,DC=vl
dn: CN=RAS and IAS Servers,CN=Users,DC=baby,DC=vl
dn: CN=Allowed RODC Password Replication Group,CN=Users,DC=baby,DC=vl
dn: CN=Denied RODC Password Replication Group,CN=Users,DC=baby,DC=vl
dn: CN=Read-only Domain Controllers,CN=Users,DC=baby,DC=vl
dn: CN=Enterprise Read-only Domain Controllers,CN=Users,DC=baby,DC=vl
dn: CN=Cloneable Domain Controllers,CN=Users,DC=baby,DC=vl
dn: CN=Protected Users,CN=Users,DC=baby,DC=vl
dn: CN=Key Admins,CN=Users,DC=baby,DC=vl
dn: CN=Enterprise Key Admins,CN=Users,DC=baby,DC=vl
dn: CN=DnsAdmins,CN=Users,DC=baby,DC=vl
dn: CN=DnsUpdateProxy,CN=Users,DC=baby,DC=vl
dn: CN=dev,CN=Users,DC=baby,DC=vl
dn: CN=Jacqueline Barnett,OU=dev,DC=baby,DC=vl
dn: CN=Ashley Webb,OU=dev,DC=baby,DC=vl
dn: CN=Hugh George,OU=dev,DC=baby,DC=vl
dn: CN=Leonard Dyer,OU=dev,DC=baby,DC=vl
dn: CN=Ian Walker,OU=dev,DC=baby,DC=vl
dn: CN=it,CN=Users,DC=baby,DC=vl
dn: CN=Connor Wilkinson,OU=it,DC=baby,DC=vl
dn: CN=Joseph Hughes,OU=it,DC=baby,DC=vl
dn: CN=Kerry Wilson,OU=it,DC=baby,DC=vl
dn: CN=Teresa Bell,OU=it,DC=baby,DC=vl
dn: CN=Caroline Robinson,OU=it,DC=baby,DC=vl
```



