---
title: Anpu - HTB Sherlocks
date: 2026-03-16 08:47:00 +0700
categories: [Forensics, Hackthebox]
tags: [Android, medium, practice]
---


> Johnny noticed something unusual on his phone after installing a new version of WhatsApp, such as tons of notifications, web pages opening, etc., and asked us to thoroughly investigate what had happened. Using various tools, we managed to extract a copy of the phone's data and discovered that the app he had installed was malicious and belonged to a well-known family of Android malware specializing in data theft. Now we need to analyze this malicious application and create a timeline for this case.<br>

Challenge provided a pcap files: `NetworkTraffic.pcap`<br>

### **Question 1. What is the source IP address of the attacker involved in this Attack?**
![All](/assets/6/1.png){: .normal }
-> 192.168.170.128

### **Question 2.How many open ports did the attacker discover on the victim's system?**

-> I used Ctrl Shift F to find Whatsapp Pro.apk in com.discord directory

```shell
44¦J4messages12600572371670180501260779976437928028  {"id":"1260779976437928028","channelId":"1260057237167018050","message":{"type":0,"content":"Hello JohNNy,\n\nI suggest you download our new version of WhatsApp, **(WhatsApp Pro)**, which includes many amazing features not found in the original WhatsApp. Key features include:\n\n- Download statuses. â¬‡ï¸ \n- Hide last seen time. ðŸ•µï¸ \n- Hide blue check marks when reading messages. â˜‘ï¸ â˜‘ï¸ \n- Hide typing status. ðŸ•µï¸ \n- Hide your view from status viewers menu. ðŸ•µï¸ \n- Get notified when a friend updates their profile picture. ðŸ””\n- Send private chat messages to up to 200 users or more. ðŸ“¨ \n- Increase chat security by locking chats with fingerprints. ðŸ” \n- Disconnect **WhatsApp Pro** from the internet. ðŸ›œ \n\n**Our WhatsApp Pro is 100% safe and secure, verified by Google Play**.\n\nHere is the APK link, just click to download and install it to enjoy the new features! ðŸ˜â¤ï¸\n\nhttps://file.io/bvWaQm6jr1Ut\n\nBest regards,\nThe WhatsApp Pro Team","mentions":[],"mention_roles":[],"attachments":[],"embeds":[{"type":"link","url":"https://file.io/bvWaQm6jr1Ut","title":"WhatsApp Pro.apk","description":"shared with file.io - super simple file sharing","thumbnail":{"url":"https://www.file.io/images/fileio-logo.png?2","proxy_url":"https://images-ext-1.discordapp.net/external/B1A_IiHv7-UjP9q20XIdlnCmQ_bMKYV55RM6GT3ZYMI/%3F2/https/www.file.io/images/fileio-logo.png","width":200,"height":200,"placeholder":"ePcNJwL3aWaHeIeKZblYh3d3iU+4tX8N","placeholder_version":1}}],"timestamp":"2024-07-11T02:09:30.542000+00:00","edited_timestamp":null,"flags":0,"components":[],"id":"1260779976437928028","channel_id":"1260057237167018050","author":{"id":"1259338416290795682","username":"whatsapppro.","avatar":"478fa31642001fc9aa75f0feb1492f15","discriminator":"0","flags":0,"banner":null,"accent_color":null,"clan":null,"publicFlags":0,"avatarDecorationData":null,"globalName":"WhatsApp Pro"},"pinned":false,"mention_everyone":false,"tts":false},"members":[],"users":[{"id":"1259338416290795682","username":"WhatsApp Pro","discriminator":"0000","avatar":"478fa31642001fc9aa75f0feb1492f15","avatarDecorationData":null,"email":null,"verified":false,"bot":false,"system":false,"mfaEnabled":false,"mobile":false,"desktop":false,"flags":0,"publicFlags":0,"purchasedFlags":0,"premiumUsageFlags":0,"phone":null,"guildMemberAvatars":{},"hasBouncedEmail":false,"personalConnectionId":null,"globalName":null,"clan":null}],"connectionId":0}1260779976437928028   	    ¥ìÛÎƒ®þqrpî«$%‚

```
![All](/assets/6/3.png){: .normal }
### **Question 3. What is the first open port that responded on the victim's system during reconnaissance?**
![All](/assets/6/3.png){: .normal }
### **Question 4. What is the CVE identifier for the vulnerability exploited by the attacker?**

-> Gemini suggested that this attack was based on the PHP CGI Argument Injection vunerability, so I researched and identified the answer.
![All](/assets/6/4.png){: .normal }

### **Question 5. What is the name and version of the vulnerable product exploited to get RCE?**
![All](/assets/6/5.png){: .normal }
### **Question 6. What is the username of the victim account?**
![All](/assets/6/6.png){: .normal }
### **Question 7. At what timestamp did the attacker execute the command to gain their initial foothold on the victim system?**
![All](/assets/6/7.png){: .normal }
### **Question 8. What is the MITRE ATT&CK technique ID used by the attacker to gain an initial foothold?**
![All](/assets/6/8.png){: .normal }
### **Question 9. What is the name of the malicious executable the attacker downloaded and executed in memory to facilitate privilege escalation on the endpoint?**
![All](/assets/6/9.png){: .normal }
### **Question 10. What is the command line used by the attacker while performing privilege escalation?**
![All](/assets/6/9.png){: .normal }
### **Question 11.The attacker failed to escalate privileges and was given an error. What is the error?**
![All](/assets/6/11.png){: .normal }




