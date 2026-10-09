## Types of Firewalls
| Firewalls                 | OSI Layers | Characteristics                                                                                                                                                                                |
| ------------------------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Stateless firewalls       | 3-4        | - Basic filtering  <br>- No track of previous connections  <br>- Efficient for high-speed networks                                                                                             |
| Stateful firewalls        | 3-4        | - Recognize traffic by patterns  <br>- Complex rules can be applicable  <br>- Monitor the network connections                                                                                  |
| Proxy firewalls           | 7          | - Inspect the data inside the packets as well  <br>- Provides content filtering options  <br>- Provides application control  <br>- Decrypts and inspects SSL/TLS data packets                  |
| Next-generation firewalls | 3-7        | - Provides advanced threat protection  <br>- Comes with an intrusion prevention system  <br>- Identify anomalies based on heuristic analysis  <br>- Decrypts and inspects SSL/TLS data packets |
## Firewall Components
- **Source address:** The machine's IP address that would originate the traffic.
- **Destination address:** The machine's IP address that would receive the data.
- **Port:** The port number for the traffic.
- **Protocol:** The protocol that would be used during the communication (TCP/UDP).
- **Action:** Action that would be taken upon identifying any traffic of this particular nature.
- **Direction:** Incoming / Outgoing

## Types of Actions
- **Allow:** traffic is permitted
- **Deny:** traffic is denied
- **Forward:** redirects traffic to a different network segment using the forwarding rules created on the firewalls

## Windows Defender Firewall
Built-in firewall on Microsoft Windows.
- **Network Profiles**
	- **Private networks:** firewall configs to apply when connected to out home network.
	- **Guest or public networks:** firewall configs when connected outside of home network.
- **Custom rules:** rules for network to allow/disallow specific traffic as needed. Can be created.