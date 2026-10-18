1. Enumerate targets
2. Discover live hosts
3. Reverse-DNS lookup
4. Scan ports
5. Detect versions
6. Detect OS
7. Traceroute
8. Scripts
9. Write Output
## Enumerating Targets
- list: `MACHINE_IP scanme.nmap.org example.com` will scan 3 IP addresses.
- range: `10.11.12.15-20` will scan 6 IP addresses: `10.11.12.15`, `10.11.12.16`,… and `10.11.12.20`.
- subnet: `MACHINE_IP/30` will scan 4 IP addresses.

## Host Live Hosts
### Using ARP
`nmap -PR -sn TARGETS`

- ARP scan is possible only if you are on the same subnet as the target systems.
- Since we can only scan the system on our subnet, we will use CONNECTION_IP to scan live hosts on the network, without any port scanning. We run `nmap -PR -sn 10.200.6.0/24` to discover all the live systems on the same subnet as our lab machine.
- Useful during post-exploitation and internal network enumeration.
- Can be used to quickly and reliably identify other live hosts on the same local segment.
- Often not filtered by firewalls (ARP operates at Layer 2).

### Using ICMP
- `nmap -PE -sn 10.200.6.0/24` to use ICMP echo
- `nmap -PP -sn 10.200.6.0/24` to use ICMP timestamp requests
- `nmap -PM -sn 10.200.6.0/24` to enable ICMP Type 17

### Using TCP and UDP
- `nmap -PS -sn 10.200.6.0/24`  for TCP SYN Ping. `-PS23` for port telnet (example)
- `sudo nmap -PA -sn 10.200.6.0/24`  for TCP ACK Ping (requires privileged account)
- `nmap -PU -sn 10.200.6.0/24` for UDP Ping

## Reverse-DNS Lookup
rDNS is the process of resolving an IP address to a hostname (the opposite of normal DNS). `-R` to query the DNS server even for offline hosts. For a specific DNS server, use `--dns-servers DNS_SERVER`.

## Scanning Ports
- Open Port: Service Running
- Closed Port: No Service
- Filtered Port: Firewall Blocking
- Blocked Port: Inaccessible

### TCP Connect scan
- `nmap -sT 10.64.137.153`
- `-F` to enable fast mode.

### TCP SYN Scan
- `nmap -sS 10.10.105.229`
- for previlege users

## UDP Scan
- `nmap -sU --top-ports 10 10.64.137.153`

### Fine tuning scope
Uses `-T<0-5>`, where the numbers represent:
- paranoid (0)
- sneaky (1)
- polite (2)
- normal (3)
- aggressive (4)
- insane (5)

Specifying port is `-p22, 80, 443` or `-p1-1023` or `-p-`. `-F` for most common 100 ports.

for setting parallelism, use `--min-parallelism <numprobes>` and `--max-parallelism <numprobes>`.

### Null Scan
The null scan does not set any flag; all six flag bits are set to zero.
- `sudo nmap -sN 10.64.130.174`

### FIN Scan
The FIN scan sends a TCP packet with the FIN flag set.
- `sudo nmap -sF 10.64.130.174`

### Xmas Scan
The Xmas scan gets its name from the Christmas tree lights. An Xmas scan sets the FIN, PSH, and URG flags simultaneously.
- `sudo nmap -sX 10.64.130.174`

## Service Detection
Further investigation of open ports is essential, as the pentester can use this information to determine whether the service has known vulnerabilities.

Adding `-sV` to your Nmap command will collect and determine service and version information for the open ports. You can control the intensity with `--version-intensity` LEVEL where the level ranges from 0(the lightest) to 9(the most complete). `-sV --version-light` has an intensity of 2, while `-sV --version-all` has an intensity of 9.

```shell-session
root@ip-10-10-166-81:~# sudo nmap -sV --version-light 10.201.118.127
sudo: unable to resolve host ip-10-10-166-81: Name or service not known
Starting Nmap 7.80 ( https://nmap.org ) at 2026-02-18 07:35 GMT
mass_dns: warning: Unable to open /etc/resolv.conf. Try using --system-dns or specify valid servers with --dns-servers
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for 10.201.118.127
Host is up (0.071s latency).
Not shown: 992 closed ports
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 9.2p1 Debian 2+deb12u3 (protocol 2.0)
25/tcp  open  smtp     Postfix smtpd
80/tcp  open  http     nginx 1.22.1
110/tcp open  pop3     Dovecot pop3d
111/tcp open  rpcbind
143/tcp open  imap     Dovecot imapd
993/tcp open  ssl/imap Dovecot imapd
995/tcp open  ssl/pop3 Dovecot pop3d
Service Info: Host:  debra2.thm.local; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.49 seconds
```

## OS Detection and Traceroute
`nmap -sS -O 10.64.140.163`

```shell-session
root@ip-10-10-166-81:~# sudo nmap -sS -O 10.64.140.163
sudo: unable to resolve host ip-10-10-166-81: Name or service not known
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-18 08:47 +0000
mass_dns: warning: Unable to open /etc/resolv.conf. Try using --system-dns or specify valid servers with --dns-servers: No such file or directory (2)
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for 10.201.118.127
Host is up (0.070s latency).
Not shown: 992 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
25/tcp  open  smtp
80/tcp  open  http
110/tcp open  pop3
111/tcp open  rpcbind
143/tcp open  imap
993/tcp open  imaps
995/tcp open  pop3s
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.98%E=4%D=2/18%OT=22%CT=1%CU=40131%PV=Y%DS=3%DC=I%G=Y%TM=69957C9
OS:D%P=x86_64-unknown-linux-gnu)SEQ(SP=103%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=
OS:A)SEQ(SP=103%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=105%GCD=1%ISR=10D%
OS:TI=Z%CI=Z%II=I%TS=A)SEQ(SP=106%GCD=1%ISR=108%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=
OS:106%GCD=2%ISR=109%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M210CST11NW7%O2=M210CST11NW
OS:7%O3=M210CNNT11NW7%O4=M210CST11NW7%O5=M210CST11NW7%O6=M210CST11)WIN(W1=F
OS:4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M
OS:210CNNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)
OS:T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S
OS:+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=
OS:Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G
OS:%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 3 hops

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.99 seconds
```

Adding `--traceroute` will find the routers between you and the target.

```shell-session
root@ip-10-10-166-81:~# nmap -sS --traceroute 10.201.118.127
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-18 08:50 +0000
mass_dns: warning: Unable to open /etc/resolv.conf. Try using --system-dns or specify valid servers with --dns-servers: No such file or directory (2)
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for 10.201.118.127
Host is up (0.071s latency).
Not shown: 992 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
25/tcp  open  smtp
80/tcp  open  http
110/tcp open  pop3
111/tcp open  rpcbind
143/tcp open  imap
993/tcp open  imaps
995/tcp open  pop3s

TRACEROUTE (using port 554/tcp)
HOP RTT      ADDRESS
1   ... 2
3   69.89 ms 10.201.118.127

Nmap done: 1 IP address (1 host up) scanned in 4.44 seconds
```

## Nmap Scripting Engine (NSE)
Nmap supports scripts written in Lua. A part of Nmap, the Nmap Scripting Engine (NSE) is a Lua interpreter that allows Nmap to execute Nmap scripts written in Lua.

Specify to use any or a group of the installed scripts. You can choose to run the scripts in the default category using `--script=default` or simply adding `-sC`.

|Script Category|Description|
|---|---|
|`auth`|Runs authentication-related scripts|
|`broadcast`|Discovers hosts by sending broadcast messages|
|`brute`|Performs brute-force password auditing against logins|
|`default`|Runs default scripts (same as `-sC`)|
|`discovery`|Retrieves accessible information, such as database tables and DNS names|
|`dos`|Detects servers vulnerable to Denial of Service (DoS)|
|`exploit`|Attempts to exploit various vulnerable services|
|`external`|Checks using a third-party service, such as Geoplugin and Virustotal|
|`fuzzer`|Launches fuzzing attacks|
|`intrusive`|Runs intrusive scripts such as brute-force attacks and exploitation|
|`malware`|Scans for backdoors|
|`safe`|Runs safe scripts that won't crash the target|
|`version`|Retrieves service versions|
|`vuln`|Checks for vulnerabilities or exploits in a vulnerable service|

```shell-session
root@ip-10-10-166-81:/usr/share/nmap/scripts# sudo nmap -sS -sC 10.201.118.127
sudo: unable to resolve host ip-10-10-166-81: Name or service not known
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-18 09:44 +0000
mass_dns: warning: Unable to open /etc/resolv.conf. Try using --system-dns or specify valid servers with --dns-servers: No such file or directory (2)
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for 10.201.118.127
Host is up (0.070s latency).
Not shown: 992 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
| ssh-hostkey: 
|   256 a3:dc:d7:6b:7e:a4:4e:fa:af:60:f6:24:78:38:16:9e (ECDSA)
|_  256 3c:e4:c0:5e:03:cb:65:aa:00:ed:29:fd:f1:d3:e6:a2 (ED25519)
25/tcp  open  smtp
|_ssl-date: TLS randomness does not represent time
|_smtp-commands: debra2.thm.local, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, CHUNKING
| ssl-cert: Subject: commonName=debra2.thm.local
| Not valid before: 2021-08-10T12:10:58
|_Not valid after:  2031-08-08T12:10:58
80/tcp  open  http
|_http-title: Welcome to nginx on Debian!
110/tcp open  pop3
|_ssl-date: TLS randomness does not represent time
|_pop3-capabilities: PIPELINING SASL RESP-CODES AUTH-RESP-CODE CAPA STLS TOP UIDL
| ssl-cert: Subject: commonName=debra2.thm.local
| Not valid before: 2021-08-10T12:10:58
|_Not valid after:  2031-08-08T12:10:58
111/tcp open  rpcbind
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|_  100000  3,4          111/udp6  rpcbind
143/tcp open  imap
| ssl-cert: Subject: commonName=debra2.thm.local
| Not valid before: 2021-08-10T12:10:58
|_Not valid after:  2031-08-08T12:10:58
|_imap-capabilities: IMAP4rev1 SASL-IR ID more have post-login LITERAL+ STARTTLS OK Pre-login capabilities listed LOGINDISABLEDA0001 LOGIN-REFERRALS IDLE ENABLE
|_ssl-date: TLS randomness does not represent time
993/tcp open  imaps
|_imap-capabilities: IMAP4rev1 SASL-IR ID OK have LITERAL+ post-login Pre-login AUTH=PLAINA0001 capabilities listed more LOGIN-REFERRALS IDLE ENABLE
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=debra2.thm.local
| Not valid before: 2021-08-10T12:10:58
|_Not valid after:  2031-08-08T12:10:58
995/tcp open  pop3s
|_ssl-date: TLS randomness does not represent time
|_pop3-capabilities: PIPELINING SASL(PLAIN) RESP-CODES AUTH-RESP-CODE CAPA USER TOP UIDL
| ssl-cert: Subject: commonName=debra2.thm.local
| Not valid before: 2021-08-10T12:10:58
|_Not valid after:  2031-08-08T12:10:58

Nmap done: 1 IP address (1 host up) scanned in 7.55 seconds
```

You can also specify the script by name using `--script "SCRIPT-NAME"` or a pattern such as `--script "ftp*"`, which would include `ftp-brute`. If you are unsure what a script does, you can open the script file with a text reader, such as `less`, or a text editor. In the case of `ftp-brute`, it states: “Performs brute force password auditing against FTP servers.” You have to be careful as some scripts are pretty intrusive. Moreover, some scripts might be for a specific server and, if chosen at random, will waste your time with no benefit. As usual, make sure that you are authorised to launch such tests on the target server.

```shell-session
root@ip-10-10-166-81:/usr/share/nmap/scripts#  sudo nmap -sS -n --script "http-date" 10.201.118.127
sudo: unable to resolve host ip-10-10-166-81: Name or service not known
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-18 09:46 +0000
Nmap scan report for 10.201.118.127
Host is up (0.077s latency).
Not shown: 992 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
25/tcp  open  smtp
80/tcp  open  http
|_http-date: Wed, 18 Feb 2026 09:46:15 GMT; +1h00m00s from local time.
110/tcp open  pop3
111/tcp open  rpcbind
143/tcp open  imap
993/tcp open  imaps
995/tcp open  pop3s

Nmap done: 1 IP address (1 host up) scanned in 1.80 seconds
```

## Saving as output
- Normal: `-oN FILENAME` (.nmap)
- Grepable: `-oG FILENAME` (.gnmap)
- XML: `-oX FILENAME` (.xml)
- Kiddie: `-oS FILENAME` (.kiddie)