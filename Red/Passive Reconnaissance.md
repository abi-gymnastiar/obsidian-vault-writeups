Refers to gathering intelligence from public sources without contacting the target. Passive recon remains one of the most powerful and lowest-risk phases in penetration testing, bug bounties, and threat hunting.

Common activities include:
- Querying public DNS records from open resolvers (A, MX, TXT, etc.).
- Searching certificate transparency logs (e.g., crt.sh) for subdomains and issued certificates
- Reviewing job postings on LinkedIn or company career pages for tech stack hints.
- Reading public news, press releases, or leaked documents on paste sites.
- Checking exposed devices via search engines like Shodan or Censys.
- Scanning public GitHub repositories for hardcoded credentials or configuration files.
## WHOIS -> RDAP

WHOIS example:

```shell-session
user@TryHackMe$ whois tryhackme.com
[Querying whois.verisign-grs.com]
[Redirected to whois.namecheap.com]
[Querying whois.namecheap.com]
[whois.namecheap.com]
Domain name: tryhackme.com
Registry Domain ID: 2282723194_DOMAIN_COM-VRSN
Registrar WHOIS Server: whois.namecheap.com
Registrar URL: http://www.namecheap.com
Updated Date: 2021-05-01T19:43:23.31Z
Creation Date: 2018-07-05T19:46:15.00Z
Registrar Registration Expiration Date: 2027-07-05T19:46:15.00Z
Registrar: NAMECHEAP INC
Registrar IANA ID: 1068
Registrar Abuse Contact Email: abuse@namecheap.com
Registrar Abuse Contact Phone: +1.6613102107
Reseller: NAMECHEAP INC
Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
Registry Registrant ID:
Registrant Name: Withheld for Privacy Purposes
Registrant Organization: Privacy service provided by Withheld for Privacy ehf
[...]
URL of the ICANN WHOIS Data Problem Reporting System: http://wdprs.internic.net/
>>> Last update of WHOIS database: 2021-08-25T14:58:29.57Z <<<
For more information on Whois status codes, please visit https://icann.org/epp
```

RDAP example:

```shell-session
user@TryHackMe$ curl -s https://rdap.verisign.com/com/v1/domain/tryhackme.com | jq .
{
  "domain": "tryhackme.com",
  "handle": "2282723194_DOMAIN_COM-VRSN",
  "ldhName": "TRYHACKME.COM",
  "status": ["clientTransferProhibited"],
  "registrar": "NAMECHEAP INC",
  "events": [
    {"eventAction": "registration", "eventDate": "2018-07-05T19:46:15.00Z"},
    {"eventAction": "last update", "eventDate": "2025-05-11T...Z"},
    {"eventAction": "expiration", "eventDate": "2034-07-05T19:46:15.00Z"}
  ],
  "nameservers": [ ... ],
  ...
}
```

## nslookup and dig
Querying DNS records.

Common DNS record types:

|Query type|Result|
|---|---|
|A|IPv4 address(es) for the domain|
|AAAA|IPv6 address(es) for the domain|
|CNAME|Canonical Name: an alias that points one domain name to another|
|MX|Mail Servers: the servers responsible for handling email for the domain|
|SOA|Start of Authority: the primary name server, admin email, and zone serial number|
|TXT|Text Records: arbitrary text, commonly used for SPF, DKIM, DMARC, and domain verification|

nslookup A example:

```shell-session
user@TryHackMe$ nslookup -type=A tryhackme.com 1.1.1.1
Server:         1.1.1.1
Address:        1.1.1.1#53

Non-authoritative answer:Name: tryhackme.comAddress: 172.67.69.208Name: tryhackme.comAddress: 104.26.11.229Name: tryhackme.comAddress: 104.26.10.229
```

nslookup MX example:
```shell-session
user@TryHackMe$ nslookup -type=MX tryhackme.com
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:tryhackme.com mail exchanger = 1 aspmx.l.google.com.tryhackme.com mail exchanger = 5 alt1.aspmx.l.google.com.tryhackme.com mail exchanger = 5 alt2.aspmx.l.google.com.tryhackme.com mail exchanger = 10 alt3.aspmx.l.google.com.tryhackme.com mail exchanger = 10 alt4.aspmx.l.google.com.....
```

dig MX example:

```shell-session
user@TryHackMe$ dig @1.1.1.1 tryhackme.com MX

; <<>> DiG 9.18.28-0ubuntu0.22.04.1-Ubuntu <<>> @1.1.1.1 tryhackme.com MX
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 12345
;; flags: qr rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 1


;; ANSWER SECTION:
tryhackme.com. 300 IN MX 1 aspmx.l.google.com.
tryhackme.com. 300 IN MX 5 alt1.aspmx.l.google.com.
tryhackme.com. 300 IN MX 5 alt2.aspmx.l.google.com.
tryhackme.com. 300 IN MX 10 alt3.aspmx.l.google.com.
tryhackme.com. 300 IN MX 10 alt4.aspmx.l.google.com.


;; Query time: 28 msec
;; SERVER: 1.1.1.1#53(1.1.1.1) (UDP)
;; WHEN: Mon Jan 26 22:00:00 UTC 2026
;; MSG SIZE rcvd: 152
```

## DNSDumpster (dnsdumpster.com)
This also reveal unadvertised subdomains like `blog.company.com` and `internal.company.com`.

## Shodan.io
Search engine for internet-connected devices. Continuously scans the public internet, collects banners and responses from open ports and services, and indexes them fro search. Shodan focuses on devices like servers, IoT, equipment, cameras, routers, industrial control systems, and more.

## cmd quick reference
| Purpose                                                | Command-line Example                              |
| ------------------------------------------------------ | ------------------------------------------------- |
| Lookup WHOIS record                                    | `whois tryhackme.com`                             |
| Lookup DNS A records (legacy)                          | `nslookup -type=A tryhackme.com`                  |
| Lookup DNS MX records at specific server (legacy)      | `nslookup -type=MX tryhackme.com 1.1.1.1`         |
| Lookup DNS TXT records (legacy)                        | `nslookup -type=TXT tryhackme.com`                |
| Lookup DNS A records (recommended)                     | `dig tryhackme.com A`                             |
| Lookup DNS MX records at specific server (recommended) | `dig @1.1.1.1 tryhackme.com MX`                   |
| Lookup DNS TXT records (recommended)                   | `dig tryhackme.com TXT`                           |
| Passive subdomain discovery (browser-based)            | Visit https://crt.sh and search `%.tryhackme.com` |