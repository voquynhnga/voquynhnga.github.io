---
title: Allegreto - HTB Sherlocks
date: 2026-03-13 14:45:00 +0700
categories: [Forensics, Hackthebox]
tags: [DFIR, medium, practice]
---


> We have been conducting surveillance on a suspected drug dealer known as Shadow. Recently, we obtained intelligence regarding his location, which led us to a flat in London. Upon executing a raid, we encountered a violent situation resulting in multiple casualties. During the operation, Shadow attempted to delete critical information from his computer to evade capture. However, we successfully apprehended him before he could cause significant damage. We have since recovered important files from his computer and would like you to analyze this data to determine his activities.<br>

Challenge provided a folder of Target, a folder of Important Collections and a folder of Modules. I used Target and Important Collections to explorer <br>

### **Question 1. Which version of QGIS is being used by Shadow?**

![All](/assets/5/1.png){: .normal }

-> I identified version of QGIS in Downloads by opening with MFTE Explorer

> QGIS is a powerful and user-friendly geographic information system that runs on Windows, macOS, Linux, Android and iOS
{: .prompt-tip }

### **Question 2. What is the Shadow's Darkmail email address?**

![All](/assets/5/2.png){: .normal }

-> In the question 2 and question 3, I use INBOX file of Thunderbird to answer. 
`C\Users\shadow\AppData\Roaming\Thunderbird\Profiles\ka48xic2.default-esr\ImapMail\mail.darkmail.htb\INBOX`

### **Question 3. What is Shadow's Bitcoin address?**
### **Question 4. What is the onion URL for the SilkRoad where Shadow sells drugs?**

-> I found answer in cookie all of Search Engine like Edge, Firefox, Internet Explorer, Tor Browser,... But I just have seen the search of 'onion+silk+road', they hadn't have the url. Many hours later, I found url in Sent of Thunderbird of Important Collections

![All](/assets/5/4.png){: .normal }


### **Question 5. Based on the cookie's last access, when was the last time Shadow visited the Silk Road website using its onion domain?**
![All](/assets/5/5.png){: .normal }

### **Question 6. What is Shadow's Hotmail email address?**

![All](/assets/5/6.png){: .normal }

### **Question 7. When was Shadow supposed to meet his mysterious boss?**

![All](/assets/5/7.png){: .normal }

### **Question 8. When was the "Contacts Due Amounts" spreadsheet created?**

![All](/assets/5/8.png){: .normal }

### **Question 9. Which file did Shadow delete from the encrypted USB drive?**
![All](/assets/5/9.1.png){: .normal }
![All](/assets/5/9.2.png){: .normal }

### **Question 10. What are Stash Roman's coordinates?**
![All](/assets/5/10.png){: .normal }

 
```shell
┌──(qnv㉿quynhngavo)-[~/ctf/HTB/Allegretto/Important collections]
└─$ sudo mkdir -p /mnt/bde_mount

┌──(qnv㉿quynhngavo)-[~/ctf/HTB/Allegretto/Important collections]
└─$ sudo bdemount -r "356400-709885-041448-681967-471328-040931-346357-184591" -o $((128 * 512)) /mnt/ewf_mount/ewf1 /mnt/bde_mount/
bdemount 20240502

┌──(qnv㉿quynhngavo)-[~/ctf/HTB/Allegretto/Important collections]
└─$ sudo ls -l /mnt/bde_mount/
total 0
-r--r--r-- 1 root root 101711872 Mar 13 22:57 bde1


┌──(qnv㉿quynhngavo)-[~/ctf/HTB/Allegretto/Important collections]
└─$ sudo mount -t ntfs-3g -o ro,loop,show_sys_files /mnt/bde_mount/bde1 /mnt/windows/
```








