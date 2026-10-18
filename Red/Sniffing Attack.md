Using a network packet capture tool to collect information about the target. When a protocol communicates in cleartext, the data exchanged can be captured by a third party to analyse.

## Where Sniffing Attacks Are Still Relevant?
- Internal corporate network
- Legacy systems
- Misconfigured services
- IoT devices
- Wireless networks
- After a successful MITM attack (strips encryption)

# Packet Capture tools
- Tcpdump
- Wireshark
- Tshark - CLI alternative to Wireshark

## Example - Capturing POP3 Credentials

```shell-session
pentester@TryHackMe$ sudo tcpdump port 110 -A
[...]
09:05:15.132861 IP 10.20.30.1.58386 > 10.20.30.148.pop3: Flags [P.], seq 1:13, ack 19, win 502, options [nop,nop,TS val 423360697 ecr 3958275530], length 12
E..@.V@.@.g.
...
......n......"............
.;....}.USER frank

09:05:15.133465 IP 10.20.30.148.pop3 > 10.20.30.1.58386: Flags [.], ack 13, win 510, options [nop,nop,TS val 3958280553 ecr 423360697], length 0
E..4..@.@.O~
...
....n....".........?P.....
...i.;..
09:05:15.133610 IP 10.20.30.148.pop3 > 10.20.30.1.58386: Flags [P.], seq 19:43, ack 13, win 510, options [nop,nop,TS val 3958280553 ecr 423360697], length 24
E..L..@.@.Oe
...
....n....".........<-.....
...i.;..+OK Password required.

09:05:15.133660 IP 10.20.30.1.58386 > 10.20.30.148.pop3: Flags [.], ack 43, win 502, options [nop,nop,TS val 423360698 ecr 3958280553], length 0
E..4.W@.@.g.
...
......n......".....??.....
.;.....i
09:05:22.852695 IP 10.20.30.1.58386 > 10.20.30.148.pop3: Flags [P.], seq 13:28, ack 43, win 502, options [nop,nop,TS val 423368417 ecr 3958280553], length 15
E..C.X@.@.g.
...
......n......".....6......
.<.....iPASS D2xc9CgD
[...]
```

## Useful tcpdump Filters

```bash
# Capture traffic on a specific port
sudo tcpdump port 110 -A

# Capture traffic to/from a specific host
sudo tcpdump host 10.20.30.148 -A

# Capture HTTP traffic (may include credentials in POST requests)
sudo tcpdump port 80 -A

# Capture FTP traffic (credentials sent in cleartext)
sudo tcpdump port 21 -A

# Write captured packets to a file for later analysis
sudo tcpdump -w capture.pcap

# Read and analyse a capture file
tcpdump -r capture.pcap -A
```

## Mitigations
- Adding an encryption layer (TLS)
- Network segmentation
- Encrypted VLANs and tunnels
- 802.1X port-based authentication
- Zero trust architecture
- Monitoring for ARP spoofing

