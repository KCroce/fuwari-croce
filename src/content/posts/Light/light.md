---
title: Light
published: 2025-07-02
description: CTF - "Welcome to the Light database application!"
image: "./light.png"
tags: [CTF, CTF_THM, CTF_THM - easy]
category: TryHackMe
draft: false
---
[THM](https://tryhackme.com/room/lightroom)

![image](./Pasted%20image%2020250702160054.png)
1.
![image](./Pasted%20image%2020250702160126.png)

Using **NMAP**
![image](./Pasted%20image%2020250702160259.png)

Port 22 (SSH) open among the first 1000 scanned ports
![image](./Pasted%20image%2020250702160512.png)

Using **SQL Injection**
![image](./Pasted%20image%2020250702164203.png)

When we enter a username, a “password” is returned.
This suggests that the system is likely using a query such as:

```sql
SELECT * FROM usuario WHERE username = 'smokey'
```

This makes it possible to use *UNION-based* SQL injection, allowing us to query a default database table from **SQLite** that stores database metadata.

Now that we have the table name, let’s test it

How many records does this table contain? (2)
- ![image](./Pasted%20image%2020250702170314.png)

Now attempting to read the discovered records
- ![image](./Pasted%20image%2020250702165326.png)

Since there are two records, we can apply the following approach:
- ![image](./Pasted%20image%2020250702170447.png)

With the usernames obtained, we can now retrieve the passwords

TryHackMeAdmin – Password
- ![image](./Pasted%20image%2020250702171327.png)

flag – Password
- ![image](./Pasted%20image%2020250702171419.png)