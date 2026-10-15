## 1. Reconnaissance
**Passive Reconnaissance**
- **WHOIS** databse to reveal contact information, registration dates, and domain owners unless hidden using privacy service add-on.
- **DNS** querying can reveal the DNS servers and public server' IP addresses.
- **Crawling & Scrapping** on the website.
- **Social media** Recons.
- **Google Dorking**, using search engines to reveal sensitive info and confidential files.

**Active Reconnaissance**
- **Network port scanning**
- **Vulnerability scanning**
- **Physical recon**, by visiting the target's premises to identify entry points, security measures, and observe personnel behavior.

**Countermeasures Against Reconnaissance**
- **Minimising public information exposure.**
- **Monitoring and anlysing network traffic and logs.**

## 2. Weaponisation
- Use exploit kits
- Preparing custom payloads
- Obfuscation
- Phising

**Countermeasure**
- check emails carefully
- user training
- disable unnecessary tools
- disabling macros on offline tools like MS Office

## 3. Delivery
- Phising emails
- Spear phising emails
- Malicious web links
- File-sharing platforms
- Malvertising
- SMS Phising (Smishing)
- Social engineering
- Physical means of delivery

**Countermeasures**
- user training in cyber security awareness
- email and web filtering on organisation level.
- Web Application Firewalls (WAFs) to block malicious files available via the web
- Network monitoring

## 4. Exploitation
- Targeting a password-based authentication system
- Use phising or other ways to trick user into submitting their password
- Exploit vulnerabilities, such as SQL Injection, buffer overflow, etc.

**Countermeasures**
- Enforcing password requierements
- Multi-factor Authentication (MFA)
- Doing a vulnerability scanning
- Intrusion Prevention Systems (IPSs) to block various exploitation attempts by inspecting the traffic for known exploits
- WAF to block attacks against web app, such as SQL Injection, XSS, CSRF.

## 5. Installation
Key word: **To make it persistence.**
- Creating scheduled tasks in MS Windows or a cron job in Linux systems
- Modify startup scripts or configuration files
- Installing new service in Windows or a daemon in Linux
- Installing malware and rootkit
- Creating backdoors
-  take advantage of the system’s built-in functions, such as legitimate Windows tools and binaries, also known as living-off-the-land binaries (LOLBins).
- Deploy a web shell

**Countermeasures**
- Endpoint Detection and Response (EDR)
- Regular system audits and comparisons against a secure baseline
- Application allowlisting

## 6. Command and Control (C2)
After the system gets compromised, C2 phase aims to establish this covert communication channel for use in the next phase.
- Using common application layer protocols such as HTTP, HTTPS, DNS, SMPTP for C2 communication.
- DNS Tunnelling
- Can use social media platforms, such as X direct messages, to send commands to a botnet as a C2 communication.
- Uses Domain Registration Algorithms (DGAs) and Fast Flux.

**Countermeasures**
- Network monitoring via firewalls, IDS, IPS to block C2 traffics
- Monitor and analyse DNS queries
- Deploying honeypots to detect and analyse C2 communication attempts and monitor attacker behaviour

## 7. Actions on Objectives
Carrying out original goals
- Ransomeware attack
- Financial theft via unauthorised wire transfers or other fraudulent transactions
- Stealing sensitive files (data exfiltration)
- Hopping to another system
- manipulating Industrial Control Systems (ICSs)

**Countermeasures**
- Data Loss Prevention (DLP)
- Network segmentation and strict access controls
- User activity monitoring to detect suspicious behaviors
- Enpoint Detection and Response (EDR)