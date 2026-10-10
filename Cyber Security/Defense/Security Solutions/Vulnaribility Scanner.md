Vulnerability scanning is the inspection of digital systems to find weaknesses.
## Authenticated vs. Unauthenticated Scans
Authenticated scans requires organization to give credentials to the scanner. While Unauthenticated scans usually only given the public facing IPs and stuff.

| Authenticated Scans                                                                                              | Unauthenticated Scans                                                                                              |
| ---------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| The credentials of the subject host must be provided to the vulnerability scanner.                               | The vulnerability scanner does not require the host’s credentials; it only needs the IP address.                   |
| Identifies the vulnerabilities that can be exploited by the attackers having access to the host.                 | Identifies the vulnerabilities that can be exploited by an external attacker having no access to the subject host. |
| It provides a deeper visibility into the target system by scanning its configuration and installed applications. | It is less resource-intensive and straightforward to set up.                                                       |
| For example, scanning an internal database by providing its credentials to the vulnerability scanner.            | For example, scanning a public-facing website for vulnerabilities that any user can exploit.                       |
## Internal vs. External Scans
Internal scans are conducted within the network, while external scans are outside the network (example: internet).

| Internal Scans                                                                                      | External Scans                                                                   |
| --------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Conducted from inside the network.                                                                  | Conducted from outside the network.                                              |
| Focuses on vulnerabilities that can be exploited inside the network.                                | Focuses on the vulnaribilities that can be exploited from outside the network.   |
| Identifies vulnerabilities that would be exposed to the attackers once they get inside the network. | Identifies the vulnerabilities exposed to the attacker from outside the network. |

## Vulnerability Scanning Tools
- **Nessus:** Proprietary software. has free version but is limited on the number of scan features.
- **Qualys:** Subscription-based.Cloud-based platform, which means there is no extra cost or effort to keep it up and running on our physical hardware and manage it.
- **Nexpose:** Subscription-based. Gives vulnerabilityu risk scores depending on the asset value and the vulnerability's impact. Also provides compliance checks against various standars. Offers on-premises and hybrid deployment modes.
- **OpenVAS:** Open-source. offers basic features with known vulnerabilities scanned through its database. less extensive than commercial tools. Good for small orgs and individual systems.
Almost all scanners offer reporting capabilities. These reports contain a list of vulnerabilities discovered, their risk scores, and detailed descriptions.

## CVE
Common Vulnerabilities and Exposures. Developed by MITRE Corporation. Whenever there is a new vulnaribility discovered in a software app, it is given a unique CVE number as a reference and published online in a CVE database. Example is `{CVE prefix} - {Year} - {Arbirary Digits}` ->  `CVE - 2024 - 9374`.
## CVSS
Common Vulnerability Scoring System. Give the CVEs a score. Calculated by considering multiple factors, including its impact, ease of exploitability, etc.

| CVSS Score Range | Severity Levels |
| ---------------- | --------------- |
| 0.0-2.9          | Low             |
| 4.0-6.9          | Medium          |
| 7.0-8.9          | High            |
| 9.0-10           | Critical        |
