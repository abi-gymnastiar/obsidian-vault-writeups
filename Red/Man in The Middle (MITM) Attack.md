Occurs when a victim (A) believes they are communicating with a legitimate destination (B) but is unknowingly communicating with an attacker (E).

Attack is relatively simple to carry out if the two parties do not confirm the authenticity and integrity of each message.

## Common Techniques
- **ARP Spoofing:** Effective on local networks. The attacker sends forged ARP messages to associate their MAC address with the IP address of the default gateway or target system. As a result, traffic intended for those systems is sent to the attacker instead.
- **DNS Spoofing:** Involves providing false DNS responses to redirect victims to attacker-controlled servers. This can happen through compromised DNS servers, DNS cache poisoning, or by responding to DNS queries faster than the legitimate server.
- **Rogue Access Points:** fake wireless access points set up by attackers. When victims connect to these networks (often named to look like legitimate networks such as `Airport_WiFi_Free`), all their traffic flows through the attacker's system.
- **BGP Hijacking:** Operates at the internet routing level, where attackers announce false BGP routes to redirect traffic through their infrastructure. This is a more sophisticated attack, typically targeting specific organisations or regions.

## Tools for MITM Attacks
- **Bettercap:** Supports ARP spoofing, DNS spoofing, HTTP/HTTPS proxying, and has a modular architecture for various attack scenarios.
- **Ettercap:** Classic tool for MITM on LANs. Replaced by Bettercap.
- **mitmproxy:** Interactive HTTPS proxy that allows inspection and modification of traffic. It is particularly useful for analysing and manipulating HTTP/HTTPS communications.
- **Responder:** Designed for Windows environments.

## MITM Against Encrypted Traffic
Approaches:
- **SSL Stripping**
- **Fake Certificates**
- **Compromised or Rogue CAs**

## Modern Defences Against MITM
- HTTPS Everywhere
- HSTS (HTTP Strict Transport Security)
- Certificate Transparency (CT)
- Certificate Pinning
- DANE (DNS-based Authentication of Named Entities)

Despite these protections, MITM attacks remain possible when:
- Users ignore certificate warnings.
- Applications do not properly validate certificates
- The target uses cleartext protocols
- The attacker can compromise a trusted CA
- Internal networks lack encryption
- Lacking modern security features on legacy systems

The solution lies in proper authentication along with encryption or signing of exchanged messages. With the help of Public Key Infrastructure (PKI) and trusted root certificates, Transport Layer Security (TLS) protects against MITM attacks when properly implemented.