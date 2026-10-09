## Netfilter
Framework inside Linux OS with core firewall functionalities; packet filtering, NAT, and connection tracking. Is the foundation for various firewall utilities available in Linux.
- **iptables:** Most widely used
- **nftables:** successor to the iptables utility, added packet filtering and NAT features.
- **firewalld:** comes with different pre-built network zone configurations.

## ufw
Uncomplicated Firewall. Making addding, removing, and viewing firewall rules easy.
To check status of firewall:
```shell-session
user@ubuntu:~$ sudo ufw status
Status: inactive
```
enabling:
```shell-session
user@ubuntu:~$ sudo ufw enable
Firewall is active and enabled on system startup
```
setting default for outgoing/incoming:
```shell-session
user@ubuntu:~$ sudo ufw default allow outgoing
Default outgoing policy changed to 'allow'
(be sure to update your rules accordingly)
```
deny an incoming port:
```shell-session
user@ubuntu:~$ sudo ufw deny 22/tcp
Rule added
Rule added (v6)
```
List in a numbered order (used for selecting and deleting)
```shell-session
user@ubuntu:~$ sudo ufw status numbered
     To                         Action      From
     --                         ------      ----
[ 1] 22/tcp                     DENY IN     Anywhere                  
[ 2] 22/tcp (v6)                DENY IN     Anywhere (v6)  
```
Deleting a rule:
```shell-session
user@ubuntu:~$ sudo ufw delete 2
Deleting:
 deny 22/tcp
Proceed with operation (y|n)? y
Rule deleted (v6)
```
