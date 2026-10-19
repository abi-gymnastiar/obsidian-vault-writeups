## Description
From Three Million Bricks to Three Million Transactions!

Brick Press Media Co. was working on creating a brand-new web theme that represents a renowned wall using three million byte bricks. Agent Murphy comes with a streak of bad luck. And here we go again: the server is compromised, and they've lost access.

Can you hack back the server and identify what happened there?

Note: Add MACHINE_IP bricks.thm to your /etc/hosts file.

## Reconnaissance
```
Starting Nmap 7.991 ( https://nmap.org ) at 2026-10-19 21:09 +0700
Nmap scan report for bricks.thm (10.66.178.185)
Host is up (0.24s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http     Python http.server 3.5 - 3.10
443/tcp  open  ssl/http Apache httpd
3306/tcp open  mysql    MySQL (unauthorized)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 54.98 seconds
```

There seems to be two web services hosted on this server. one HTTP and the other HTTPS.

### Port 80 - `http://bricks.thm`
![](../Image%20Trunk/Pasted%20image%2020261019211521.png)

### Port 443 - `https://bricks.thm`
![](../Image%20Trunk/Pasted%20image%2020261019214559.png)

- Wappalyzer shows that it's a wordpress website.

## Scanning for vulnerabilities for the specific wordpress version
We can scan for vulnerabilities within a wordpress website using `WPScan`, a dedicated vulnerability and pentesting scanner for wordpress.

You need a valid wpscan api token key in order to scan for vulnerabilities. Run `sudo wpscan --url "https://bricks.thm" --disable-tls-checks --api-token "API_KEY" -o brickscan.txt`

The most interesting part about the output is this:
```
[+] WordPress theme in use: bricks
 |
 | [!] Title: Bricks < 1.9.6.1 - Unauthenticated Remote Code Execution
 |     UUID: afea4f8c-4d45-4cc0-8eb7-6fa6748158bd
 |     Fixed in: 1.9.6.1
 |     References:
 |      - https://wpscan.com/vulnerability/afea4f8c-4d45-4cc0-8eb7-6fa6748158bd
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-25600
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/b97b1c86-22a4-462b-9140-55139cf02c7a
 |
```

After reading about it for a bit, turns out it is an injection vulnerability (OWASP A1).

## Exploiting CVE-2024-25600
We'll be using metasploit for this exploit.

![](../Image%20Trunk/Pasted%20image%2020261020001155.png)

After that we make sure to have the correct options for the payload (we'll be trying the default payload first).

![](../Image%20Trunk/Pasted%20image%2020261020001421.png)

then, simply run `exploit` to send the payload and hopefully we get the shell.

![](../Image%20Trunk/Pasted%20image%2020261020004637.png)

No luck :(

Luckily, I found a proof of concept of the cve from this github repo: https://github.com/Chocapikk/CVE-2024-25600.git. Using the provided python script, we easily get the shell!

![](../Image%20Trunk/Pasted%20image%2020261020004833.png)

the first flag is in the `650c844110baced87e1606453b93f22a.txt` file.

## Suspicious process
The second question is `What is the name of the suspicious process?`. We can find it using `systemctl` and pipe it to `grep running` or something.

![](../Image%20Trunk/Pasted%20image%2020261020005625.png)

![](../Image%20Trunk/Pasted%20image%2020261020005826.png)

from here, the answer to the question is `nm-inet-dialog`. This also answers the next question, `What is the service name affiliated with the suspicious process?`, which is `ubuntu.service`.

## Tracing the service

Todo...