## Reconnaissance
```
# Nmap 7.993 scan initiated Tue Nov 17 21:34:24 2026 as: nmap -sV -o nmap-scan.txt 10.129.182.143
Nmap scan report for 10.129.182.143
Host is up (0.25s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 10.6 (protocol 2.0)
80/tcp open  http    nginx 1.24.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Nov 17 21:35:08 2026 -- 1 IP address (1 host up) scanned in 44.13 seconds
```

## CVE-2026-123158
Looking up the OpenSSH 10.6 vulnerabilities, I found a cve that causes `crypt()` function to fail and return an asterisk. This allow us to get to locked account's shadow entry.

![](../Image%20Trunk/Pasted%20image%2020261117221707.png)

We can get in by providing a 300 bytes password. This will result in access denied, but we actually clobbered the buffer. Typing another 300 bytes password will grant us the shell.

## Firefox folder

![](../Image%20Trunk/Pasted%20image%2020261117222105.png)

Looking inside the directories, I found `logins.json` inside `.mozilla/firefox/zentara.default/`. This has a data of logged-in user.

```json
{
  "nextId": 2,
  "logins": [
    {
      "id": 1,
      "hostname": "http://172.17.0.1",
      "httpRealm": "Zentara",
      "formActionOrigin": null,
      "usernameField": "",
      "passwordField": "",
      "encryptedUsername": "Ym9i",
      "encryptedPassword": "Q29ycmVjdEhvcnNlQmF0dGVyeVN0YXBsZQ==",
      "guid": "{a1b2c3d4-e5f6-7890-abcd-ef1234567890}",
      "encType": 0,
      "timeCreated": 1730000000000,
      "timeLastUsed": 1730900000000,
      "timePasswordChanged": 1730000000000,
      "timesUsed": 14
    }
  ],
  "potentiallyVulnerablePasswords": [],
  "dismissedBreachAlertsByLoginGUID": {},
  "version": 3
}
```

We can then decode the "encryptedUsername" and "encryptedPassword" from `base64` and we got `bob` and `CorrectHorseBatteryStaple`.

I then tried ssh again as bob. It works, and inside,  there was the first user flag.
## Using the new credential on website
Using Bob's credentials, we can get in to the website:

![](../Image%20Trunk/Pasted%20image%2020261117222821.png)

![](../Image%20Trunk/Pasted%20image%2020261117224436.png)

Looking around the website, I found a deploy file that accept a compressed file to be extracted into the agent workspace.