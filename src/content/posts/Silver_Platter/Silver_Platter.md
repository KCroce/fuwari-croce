---
title: Silver Platter
published: 2025-07-01
description: CTF - "Can you breach the server?"
image: "./silver.png"
tags: [CTF, CTF - easy]
category: TryHackMe
draft: false
---

[THM](https://tryhackme.com/room/silverplatter)

# 1. NMAP
![image](./Pasted%20image%2020250704144735.png)

--- 
# 2. GoBuster
I then ran **Gobuster** on port 8080.
![image](./Pasted%20image%2020250704145948.png)

## Results

![image](./Pasted%20image%2020250704151833.png)

`/website/`
  * ![image](./Pasted%20image%2020250704151213.png)

`/noredirect.html`

  * ![image](./Pasted%20image%2020250704151258.png)

---
# 3. Reasoning with what we have
It is understood that we have two web ports open (80/8080).
On port 80, we have the website:

* ![image](./Pasted%20image%2020250704152849.png)

 Now, on:
-  ![image](./Pasted%20image%2020250704153020.png)

  *“If you want to contact us, please look for our project manager on Silverpeas. His username is scr1ptkiddy.”*

- ![image](./Pasted%20image%2020250704153043.png)

What is **Silverpeas**?

  *Silverpeas is an open-source collaboration and content management platform (ECM – Enterprise Content Management), mainly aimed at corporate or institutional environments. The system provides a modular set of features that facilitate communication, document sharing, and internal process management within an organization.*

![image](./Pasted%20image%2020250704154328.png)

So this is an organizational software
We found:
- ![image](./Pasted%20image%2020250704152223.png)

Technology updated until 2022?
- ![image](./Pasted%20image%2020250704162020.png)

## Searching for exploits
![image](./Pasted%20image%2020250704162358.png)
  * [LINK](https://github.com/advisories/GHSA-4w54-wwc9-x62c)
    -   ![image](./Pasted%20image%2020250704165255.png)
  * [LINK](https://gist.github.com/ChrisPritchard/4b6d5c70d9329ef116266a6c238dcb2d)
    -   ![image](./Pasted%20image%2020250704165800.png)

---
# Exploit Bypass
Removing the highlighted part and logging in
![image](./Pasted%20image%2020250704170531.png)

Access granted:
![image](./Pasted%20image%2020250704170638.png)

---
# CVE
Now with administrator access, I searched for exploits that could be used with proper privileges.

**Rhino Security**
- [https://rhinosecuritylabs.com/research/silverpeas-file-read-cves/](https://rhinosecuritylabs.com/research/silverpeas-file-read-cves/)
    -![image](./Pasted%20image%2020250705144651.png)
[LINK](https://github.com/RhinoSecurityLabs/CVEs/tree/master/CVE-2023-47323)
- ![image](./Pasted%20image%2020250705144721.png)

**Instructions**
- ![image](./Pasted%20image%2020250705144936.png)

**Usage**
- ![image](./Pasted%20image%2020250705145059.png)

---
# Privilege Escalation (tim → tyler)
1. Identifying the current user
* ![image](./Pasted%20image%2020250705145949.png)

2. Which users exist on the system?
* ![image](./Pasted%20image%2020250705153552.png)

3. `cat /var/log/auth* | grep -i pass`
   1. `cat /var/log/auth*`
      * cat: Command used to display the contents of files.
      * /var/log/auth*: Path to authentication log files:
        * The asterisk (*) is a wildcard that includes all files starting with auth in /var/log/ (e.g., auth.log, auth.log.1, auth.log.2.gz, etc.).
        * These logs record authentication-related events (logins, sudo, SSH, etc.).

   2. `|` (pipe)
      * Redirects the output of the previous command (cat) to the next command (grep).

   3. `grep -i pass`
      * grep: Tool used to search for patterns in text.
      * -i: Case-insensitive mode (ignores uppercase/lowercase).
      * pass: Search term (can match “pass”, “Pass”, “PASSWORD”, etc.).

4. Command output
   ![image](./Pasted%20image%2020250705151118.png)

Let’s test this password:
   `su tyler`
*  ![image](./Pasted%20image%2020250705151219.png)

Success!
* ![image](./Pasted%20image%2020250705151501.png)

---
# Privilege Escalation (tyler → root)

* Understanding the user
  ![image](./Pasted%20image%2020250705152232.png)

* The user has sudo privileges
  * `sudo su`
  * ![image](./Pasted%20image%2020250705154234.png)

* Now searching for the flags
  *(I always recommend checking the user’s home directory)*
        * ![image](./Pasted%20image%2020250705154339.png)