Is the process of directly interacting with a target system or network to gather information about it.

Common activities include:
- Sending packets to discover live hosts (e.g., ICMP pings, ARP requests).
- Port scanning or service enumeration (Nmap, masscan).
- Interacting with web applications or APIs (fuzzing endpoints, directory brute-forcing).
- Social engineering attempts (phising, vishing, pretexting phone calls).
- Physical approaches (tailgating, posing as a vendor).

## ping
```bash
user@AttackBox$ ping -c 5 10.67.153.231
PING 10.67.153.231 (10.67.153.231) 56(84) bytes of data.
64 bytes from 10.67.153.231: icmp_seq=1 ttl=64 time=0.512 ms
64 bytes from 10.67.153.231: icmp_seq=2 ttl=64 time=0.478 ms
64 bytes from 10.67.153.231: icmp_seq=3 ttl=64 time=0.491 ms
64 bytes from 10.67.153.231: icmp_seq=4 ttl=64 time=0.503 ms
64 bytes from 10.67.153.231: icmp_seq=5 ttl=64 time=0.485 ms

--- 10.67.153.231 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4098ms
rtt min/avg/max/mdev = 0.478/0.494/0.512/0.012 ms
```

Output reference:

|Result|Most likely meaning|Next step|
|---|---|---|
|Fast replies, low or no packet loss|Target is online and allows ICMP|Proceed to port scanning|
|"Destination Host Unreachable"|Target is down or no route exists|Check if machine is powered on|
|100% packet loss with no error message|ICMP is filtered or blocked|Try TCP/UDP host discovery with Nmap|
|High latency or heavy loss|Network congestion, long distance, or filtering|Investigate the path with `traceroute`|

## traceroute
Traces the route that packets take from your system to a target host. Discover the IP addresses of the routers (hops) along the path and to determine how many of them sit between you and the destination.

Sends UDP datagram by default. Can be switched to TCP-based tracing, which is useful to bypass a UDP filters.

## telnet
Telnet protocol communicate with a remote system via a cli. `telnet` command uses this protocol for remote administration, with a default port of 23. Now it is replaced with SSH, which encrypts all traffic.

netcat (nc) and curl are generally preferred alternative that provides similar functionality with more flexibility.

Example:

```shell-session
pentester@TryHackMe$ telnet 10.67.153.231 80
Trying 10.67.153.231...
Connected to 10.67.153.231.
Escape character is '^]'.
GET / HTTP/1.1
host: telnet

HTTP/1.1 200 OKServer: nginx/1.6.2Date: Tue, 17 Aug 2021 11:13:25 GMTContent-Type: text/htmlContent-Length: 867Last-Modified: Tue, 17 Aug 2021 11:12:16 GMTConnection: keep-aliveETag: "611b9990-363"Accept-Ranges: bytes...
```

In modern environments, many services enforce encryption. For example, SMTPS runs on port 465, and HTTPS runs on port 443. Telnet cannot handle encrypted connections. For HTTPS, use `curl --head https://10.67.153.231` or `openssl s_client -connect 10.67.153.231:443`. For other TLS-wrapped services, `openssl s_client` or `ncat --ssl` are the appropriate tools.

## netcat (nc)
A versatile networking utility that supports both TCP and UDP protocols. It can function as a client that connects to a listening port, or as a server that listens on a port of your choice. Useful for banner grabbing, port probing, simple file transfers, and basic client-server communication.

`ncat` is the modern version. It supports IPv6 and SSL encryption, making it more versatile than legacy tools like telnet.

Example Banner Grabbing:

```shell-session
pentester@TryHackMe$ nc 10.67.142.242 80
GET / HTTP/1.1
host: netcat

HTTP/1.1 200 OKServer: nginx/1.6.2Date: Tue, 17 Aug 2021 11:39:49 GMTContent-Type: text/htmlContent-Length: 867Last-Modified: Tue, 17 Aug 2021 11:12:16 GMTConnection: keep-aliveETag: "611b9990-363"Accept-Ranges: bytes...
```

Listening with Netcat:
- run `nc -vnlp 1234` to start listening on port 1234.
- run `nc TARGET_IP 1234` to connect to it.

| Option | Meaning                                      |
| ------ | -------------------------------------------- |
| `-l`   | Listen mode                                  |
| `-p`   | Specify the port number                      |
| `-n`   | Numeric only; no DNS resolution of hostnames |
| `-v`   | Verbose output, useful for debugging         |
| `-vv`  | Very verbose output                          |
| `-k`   | Keep listening after the client disconnects  |

## Quick references
|Command|Example|
|---|---|
|ping|`ping -c 10 10.67.142.242` on Linux or macOS|
|ping|`ping -n 10 10.67.142.242` on Windows|
|ping (IPv6)|`ping -6 MACHINE_IPV6` or `ping6 MACHINE_IPV6`|
|traceroute|`traceroute 10.67.142.242` on Linux or macOS|
|tracert|`tracert 10.67.142.242` on Windows|
|traceroute (IPv6)|`traceroute -6 MACHINE_IPV6` or `traceroute6 MACHINE_IPV6`|
|mtr|`mtr 10.67.142.242` for real-time path monitoring|
|telnet (legacy)|`telnet 10.67.142.242 PORT_NUMBER`|
|netcat as client|`nc 10.67.142.242 PORT_NUMBER`|
|netcat as server|`nc -lvnp PORT_NUMBER`|
|netcat (IPv6)|`nc -6 MACHINE_IPV6 PORT_NUMBER`|
|curl for HTTP banner|`curl -I http://10.67.142.242` or `curl -I https://10.67.142.242`|
