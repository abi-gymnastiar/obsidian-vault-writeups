- Link: https://tryhackme.com/room/support
- Difficulty: Medium
- Category: Web Exploitation

## Descripton
A new internal **Support Operations Platform** has been deployed to assist IT and helpdesk teams. The application handles user management, internal APIs, and system-level operations. However, security was not the primary focus during development. Several features rely on user-controlled input and weak trust boundaries.

_![Pentest and exploit the web application.](https://cdn-images.tryhackme.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/62a7685ca6e7ce005d3f3afe-1773827665267.png)_

_Can you pentest the platform and escalate your access to achieve RCE on the server?_

## Reconnaissance
### Nmap port scan:

```
# Nmap 7.992 scan initiated Tue Oct 27 23:55:29 2026 as: nmap -sV -o portscan.txt 10.65.132.221
Nmap scan report for 10.65.132.221
Host is up (0.25s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.11 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.58 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Oct 27 23:56:07 2026 -- 1 IP address (1 host up) scanned in 38.23 seconds
```

### Website tech stack (wappalyzer):

![](Image%20Trunk/Pasted%20image%2020261028000108.png)

- Web servers: Apache HTTP Server 2.4.58
- Programming languages: PHP
- Operating systems: Ubuntu

### Directory Enumeration (gobuster)
```
index.php            (Status: 200) [Size: 2591]
info.php             (Status: 200) [Size: 73301]
footer.php           (Status: 200) [Size: 1253]
skins                (Status: 301) [Size: 314] [--> http://10.65.132.221/skins/]
includes             (Status: 301) [Size: 317] [--> http://10.65.132.221/includes/]
layout               (Status: 301) [Size: 315] [--> http://10.65.132.221/layout/]
js                   (Status: 301) [Size: 311] [--> http://10.65.132.221/js/]
api.php              (Status: 302) [Size: 0] [--> index.php]
logout.php           (Status: 302) [Size: 0] [--> index.php]
config.php           (Status: 200) [Size: 0]
```

`/includes`, `/skins`, and `layout` are all directory listing, which could be useful later on.
## Finding Vulnerabilities
![](Image%20Trunk/Pasted%20image%2020261028000639.png)

In the login page, we can see the "Corporate Email" has a pre-filled value "help@support.thm". Under the "Sign In" button, there is also a note "Contact IT Operations @ help@support.thm". First thing that we should check is to whether they implemented rate-limiting on login attempts.

For this, I fired up burp suite and intercept the POST request to login. Then, sending it out to *Repeater* and try hitting the send button multiple times to see the results. After 10+ attempts, it does not seems they implemented rate-limiting.

## Exploiting broken authenticator

Next, I send the POST to the *Intruder*. Here, I planned to do a password enumeration using `rockyou.txt` against `help@support.thm` email.

![](Image%20Trunk/Pasted%20image%2020261028001405.png)

Using the configuration above, I started the attack using "Sniper attack" mode. After 139 requests sent, we got our password:

![](../Image%20Trunk/Pasted%20image%2020261028002733.png)

302 above means that using that payload we got redirected somewhere. using the password, and we're logged in as `Helpdesk User` and got redirected to `/dashboard.php`:

![](../Image%20Trunk/Pasted%20image%2020261028003000.png)

## Lateral movement

There seems like not much we can do here other than logout and changing theme.

![](../Image%20Trunk/Pasted%20image%2020261028004028.png)

![](../Image%20Trunk/Pasted%20image%2020261028003720.png)

Interesting find is that selecting a different theme will actually send a GET request.

![](../Image%20Trunk/Pasted%20image%2020261028003849.png)

Now if we check the directory lising at `/skins`, we can see that the themes are all stored here!

![](../Image%20Trunk/Pasted%20image%2020261028004149.png)

Now we could actually guess that the `/dashboard.php?skin=red` is actually being processed to lookup these files. Next, I tried to use some sort of path traversal methods to exploit this file inclusions.

Trying out `../../../etc/passwd%00` and other similar values resulted in nothing being output-ed in the response. I'll hold this for now and look into other stuff.

Another important thing I found is that we actually stored a cookie `isITUser=68934a3e9455fa72420237eb05902327` alongside the usual `PHPSESSID`. Using https://crackstation.net/ we got our actual value `false`:

![](../Image%20Trunk/Pasted%20image%2020261028010145.png)

Next, is as simple as trying `md5` hashed value of `true` to be put as `isITUser` value:

![](../Image%20Trunk/Pasted%20image%2020261028010539.png)

Now we got admin authorisation! clicking `View API` redirected us to `/api.php`. Here, only one API documentation is listed:

![](../Image%20Trunk/Pasted%20image%2020261028010737.png)

Trying it out other user id like `http://10.65.132.221/user/1` resulted in us getting the admin's profile info:

```json
{
    "email": "specialadmin@support.thm",
    "2FA": false,
    "admin": true
}
```

We don't have the password yet though. So next thing to do is to fire up *Intruder* again in BurpSuite to enumerate password usin `rockyou.txt` wordlist.

BurpSuite too slow, switched to hydra. Still no luck after 3000+ passwords. Seems like it is not a weak password.

TBC.