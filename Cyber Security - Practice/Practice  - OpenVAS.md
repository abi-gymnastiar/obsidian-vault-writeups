## Background
- Learn about vulnerability scanning by using OpenVAS.
- Check on any vulnerability in my homelab.
- OpenVAS will be deployed on a different machine connected to the same local network with the target.
- OpenVAS will be deployed on a docker.

## Hosts
- target: 192.168.0.251 (enp3s0) - Used as target
- scanner: 192.168.0.10 (enp3s0)- Used as scanner

## Pre-Scan
1. Make sure yipdesktop and yipserver are online and connected in the same local network
   ![Pasted image 20261010163820.png](../Image%20Trunk/Pasted%20image%2020261010163820.png)
2. Deploy OpenVAS inside docker container, using immauss/openvas
   ![Pasted image 20261010175234.png](../Image%20Trunk/Pasted%20image%2020261010175234.png)
3. Access port 9392 on localhost and login
   ![Pasted image 20261010185258.png](../Image%20Trunk/Pasted%20image%2020261010185258.png)
## Scanning
1. Add a new target (yipserver) and new Task, targetting it. Set the scanner to OpenVAS default and scan config to Full and Fast.
2. Start the task. Seems to be able to look at the report generated status from the task list.
   ![Pasted image 20261010204529.png](../Image%20Trunk/Pasted%20image%2020261010204529.png)
3. Just wait for it to done finishing I guess.
## Post-Scan
1. After around 10 minutes, the status changed to done 100%.
2. Looking at the task detail, There is a single report generated and 129 results.
3. Upon closer inspection, most of the report seems to be just logs.
   ![Pasted image 20261010210031.png](../Image%20Trunk/Pasted%20image%2020261010210031.png)
4. There are two results that is not considered as Log. One low severity and one medium severity.
5. Looking at the low severity, it turns out to be a TCP timestamp vulnerability.
   ![Pasted image 20261010210210.png](../Image%20Trunk/Pasted%20image%2020261010210210.png)
   Seems like impact of this vulnerability is that the uptime of the remote host can sometimes be computed. After researching more about this **RFC 1312**, seems like there is no need for me to address it on my homelab. Usually, this becomes more of a concern on systems governed by regulatory compliance and public-facing network edge devices. Finding out the time my homelab last restarted is not important.
6. Looking at the other result, shows that it is clasified as a medium severity. Turns out to be "Cleartext Transmission of Sensitive Information via HTTP"
   ![Pasted image 20261010212216.png](../Image%20Trunk/Pasted%20image%2020261010212216.png)
   After closer inspection, port 8083 is from my qbittorrent web ui client. After researching more about this (CWE-319), having plaintext credentials is a risk if the WebUI is configured to use plain HTTP instead of HTTPS/TLS. Network Sniffers can allow anyone sharing the same local network, public Wi-Fi, or compromised router to intercept traffic.
8. Workaround for CWE-319 on my qbittorrent client is as simple as to close the port and use a reverse proxy (in my case nginx proxy manager) to route the traffic exclusively thru HTTPS into the shared docker network.
   Before:
   User (Browser) ── HTTP (Cleartext) ──> Host Port 8083 ──> qBittorrent Container
   After:
   User (Browser) ──HTTPS (Encrypted) ──> Nginx (Host Port 443) ── Internal Docker Network ──> qBittorrent Container

## Reflections
This practical experiments made me more understand about some of the vulnerabilities that can affects a homelab system. I ended up being more familiar and confortable in doing vulnerability scans using OpenVAS.

## What's Next?
To continue improve in Cyber Security by doing more practical experiments.