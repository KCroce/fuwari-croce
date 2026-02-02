---
title: Vulnversity
published: 2025-02-04
description: CTF - "Learn about active recon, web app attacks and privilege escalation."
image: "./vulnversity.png"
tags: [CTF, CTF - easy]
category: TryHackMe
draft: false
---

[THM](https://tryhackme.com/room/vulnversity)
## 1. Scanning for open ports with NMAP
1. ![image](Pasted%20image%2020250204114657.png)
## 2. Enumerating directories on port 3333 with GoBuster
1. ![image](Pasted%20image%2020250204121129.png)
## 3. Using BurpSuite
1. The first step was to access the page that accepts file uploads.
    ![image](Pasted%20image%2020250218222643.png)

        
2. Now that I know where the page accepts files, I performed a test using Burp.  
    First, I captured an example request in the Proxy tab (just try to upload a file and capture the request):
    1. ![image](Pasted%20image%2020250218223104.png)
        
3. Then I used the Sniper attack, modifying only a specific part of this request:
    1. ![image](Pasted%20image%2020250218223215.png)
        
4. By adding the symbols, the loaded payloads will be injected at that position.
    1. ![image](Pasted%20image%2020250218223330.png)
        
5. Now we analyze the response:
    1. ![image](Pasted%20image%2020250219003139.png)
        
    2. From this, we know which file types are accepted by the input.
        

## 4. Now that I know which file types the input accepts, I used a .phtml file to gain access to the machine

(File from: [Github](https://github.com/pentestmonkey/php-reverse-shell)) and upload it to the server to obtain a reverse shell. Some changes are required in the file:

1. Since we are on the TryHackMe VPN, we set the corresponding IP address.  
    ![image](Pasted%20image%2020250219013240.png)
2. With the file uploaded to the server, I found the directory where uploaded files are stored and accessed the malicious file.  
    After that, we establish the connection as shown below:
    1. ![image](Pasted%20image%2020250219014146.png)
    

## 5. To retrieve the flag, I entered /home/bill and found a file containing a code.

## 6. Privilege escalation in this case
1. Searching inside the root directory:
    
    1. ![image](Pasted%20image%2020250219081656.png)
    
2. We will use a bash reverse shell:
    1. We create a file to escalate privileges (root.service).  
        ![image](Pasted%20image%2020250219172536.png)
        
    2. Then we use wget from inside the target machine, pointing to a simple HTTP server.
        1. Server:
            ![image](Pasted%20image%2020250219173313.png)
        2. Downloading the malicious file (root.service).  
            We must be in a directory where we have write permission (/tmp).
            1. ![image](Pasted%20image%2020250219180320.png)
        3. Using wget to download the malicious file from the server:
            1. ![image](Pasted%20image%2020250219180441.png)

    3. Enabling this file with systemctl:
        1. ![image](Pasted%20image%2020250219180722.png)

    4. Starting the service:
        1. ![image](Pasted%20image%2020250219181118.png)
    
## 7. Connecting with the reverse shell file
(You must be listening for the connection while starting root.service)

1. ![image](Pasted%20image%2020250219180911.png)
2. ![image](Pasted%20image%2020250219181401.png)

## 8. Finally, we capture the last flag
1. ![image](Pasted%20image%2020250219181616.png)