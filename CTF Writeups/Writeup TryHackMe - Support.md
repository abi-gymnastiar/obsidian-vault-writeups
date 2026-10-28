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

## Path Traversal - Arbitrary File Read

There seems like not much we can do here other than logout and changing theme.

![](../Image%20Trunk/Pasted%20image%2020261028004028.png)

![](../Image%20Trunk/Pasted%20image%2020261028003720.png)

Interesting find is that selecting a different theme will actually send a GET request.

![](../Image%20Trunk/Pasted%20image%2020261028003849.png)

Now if we check the directory lising at `/skins`, we can see that the themes are all stored here!

![](../Image%20Trunk/Pasted%20image%2020261028004149.png)

Now we could actually guess that the `/dashboard.php?skin=red` is actually being processed to lookup these files. Next, I tried to use some sort of path traversal methods to exploit this file inclusions.

Trying out `../../../etc/passwd%00` and other similar values resulted in nothing being output-ed in the response. I'll hold this for now and look into other stuff.

`important note: '%00' trick only works on php version < 5.3.4.`

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

Next, I went back to the previous **LFI** exploit attempts. Using BurpSuite's *Repeater*, I tried the other `.php` files that I can access thru the directory listing and enumeration.

`config.php` is a very interesting file. Upon using `../config` as the skin parameter, we actually get an interesting variable, `$MASTER_PASSWORD = 'support@110';`

![](../Image%20Trunk/Pasted%20image%2020261028193846.png)

`Important note: the actual master password is 'support110' without the '@' character. The room's creator made a mistake`.

Using the previously fetched `specialadmin@support.thm` email, I tried logging in with the master password. This resulted in a successful login and a different look on the `/dashboard.php` page, now including the first flag.

![](../Image%20Trunk/Pasted%20image%2020261028194235.png)

## RCE
Another very interesting difference within the admin's version of `/dashboard.php` is the new `Date` drop down besides theme selector. Upon selecting `Date` from the new drop down, a POST request was created, and an output was printed on the page:

```
POST /dashboard.php HTTP/1.1
Host: 10.67.174.234
Content-Length: 8
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/151.0.0.0 Safari/537.36
Origin: http://10.67.174.234
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.67.174.234/dashboard.php
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=pte4vt1ikvitnm6uhn4quv7ip7; isITUser=b326b5062b2f0e69046810717534cb09
Connection: keep-alive

sys=date
```

![](../Image%20Trunk/Pasted%20image%2020261028194646.png)

Interestingly, if we actually try to do the previous path traversal into the `footer.php` using `?skin=../footer.php`, we get an interesting code snippets:

```php
<?php
$isAdmin = $_SESSION['admin'];

$output = '';
$error  = '';


$selectedSys = 'date';

if ($isAdmin && $_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['sys'])) {

    $selectedSys = $_POST['sys'];
    $sys = $_POST['sys'];


    if (strpos($sys, 'date') === 0) {
        $output = shell_exec($sys); 
    } else {
        $error = 'Only date command is allowed.';
    }
}
?>
```

This logic is vulnerable to a **command injection**, by just appending `; <system command>;` int the `sys` body parameters. This works because the code just accept the value as long as it has the string `date` in them.

Trying `date; cat /etc/passwd;` resulted in the actual content being spat out into the output:

![](../Image%20Trunk/Pasted%20image%2020261028195010.png)

Lastly, since we know the last flag's file path (`/home/ubuntu/user.txt`), we can just change `/etc/passwd` into `/home/ubuntu/user.txt` and we get the flag!

![](../Image%20Trunk/Pasted%20image%2020261028195249.png)

## Root Causes and Remediation
These exploits are possible because of the existance of the following vulnerabilities:
- **Broken Authentication**: Lack of rate limiting, combined with a weak password.
- **Broken Object Level Authorization (BOLA):** Being able to access other user's information by simply enumerating `GET /user/{id}` API.
- **Path Traversal (Arbitrary File Read):** Lack of proper allowlist for the skin parameter, combined with a validation check that only confirms the resolved path starts within the webroot rather than restricting it to the intended skins/ directory, allows reading arbitrary .php source files on the server.
- **Remote Code Execution:** Weak input sanitasion on `sys` body parameter inside `POST /dashboard.php` leads to user being able to execute system commands.

Some of the remediation that should be implemented:
- Implement rate limiting on login request.
- Enforce a strong password combinations.
- Implement proper authorisation on APIs.
- Implement proper allowlist for `skin` parameter.
- Implement proper input sanitasion on user's parameter request inputs.