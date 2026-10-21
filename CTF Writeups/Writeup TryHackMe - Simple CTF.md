## Reconnaissance
#### Port Scan
![](../Image%20Trunk/Pasted%20image%2020261021174450.png)

#### Directory Enumeration
![](../Image%20Trunk/Pasted%20image%2020261021182253.png)

## Looking for Velnerabilities
Enumerating the apache site using gobuster finds `/simple` directory, which is a "CMS Mad Simple" landing page.

![](../Image%20Trunk/Pasted%20image%2020261021180449.png)

On the footer, there is a line which stated the exact version of this.

![](../Image%20Trunk/Pasted%20image%2020261021180620.png)

Next thing is doing a google search to look for a known vulnerabilities of this version. This took me to an NIST article about `CVE-2019-9053`, which is an SQL Injection. 

![](../Image%20Trunk/Pasted%20image%2020261021181045.png)

## Exploiting
For this, I used script from here: https://www.exploit-db.com/exploits/46635

![](../Image%20Trunk/Pasted%20image%2020261021193113.png)

there were some problems with the scripts. However, we already have both the password and the salt. Cracking the password is as simple as using john the ripper.

![](../Image%20Trunk/Pasted%20image%2020261021200326.png)

## Logging in as mitch
To login, we can head over to  `/simple/admin/login.php` and use mitch:secret as the credentials.

![](../Image%20Trunk/Pasted%20image%2020261021200552.png)

More importantly, we can actually ssh into the machine as mitch. Note that the port for this ssh is on 2222.

![](../Image%20Trunk/Pasted%20image%2020261021201311.png)

![](../Image%20Trunk/Pasted%20image%2020261021201444.png)

Checking our sudo privilege with `sudo -l` showed that we have access to `vim`. Which is helpful. We can also see there is another user named `sunbath` inside the `/home/` directory.

As for the root flag, we can simply use `sudo vim /root/` and it will take us to a directory selector interface. Which we can then open to get our last flag.

![](../Image%20Trunk/Pasted%20image%2020261021201829.png)