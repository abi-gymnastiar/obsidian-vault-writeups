## Description
Check out our new cloud service, Authentication Anywhere. Can you find other user's secrets?

## Recon
- `nmap -sV -sC 10.64.149.58 `
  ![](../Image%20Trunk/Pasted%20image%2020261016192019.png)
	- 22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
	- 80/tcp open  http    Apache httpd 2.4.53 ((Debian))
- Website (port 80)
  ![](../Image%20Trunk/Pasted%20image%2020261016192102.png)
	- `/` is a login form
	- Can use guest account

## Navigating the website
- `/` -> login form
- `CTRL+C` results in viewing the source code (html).
  ![](../Image%20Trunk/Pasted%20image%2020261016212725.png)
- Trying `username: guest` and `password: guest` results in successful login
  ![](../Image%20Trunk/Pasted%20image%2020261016212834.png)

## Exploiting IDOR Attempt
- Since the GET HTTP has a plaintext parameter `user=guest`, We can try changing the user value to another user. Here, I'll try to use `admin`.
  ![](../Image%20Trunk/Pasted%20image%2020261016213013.png)
- Flag found!