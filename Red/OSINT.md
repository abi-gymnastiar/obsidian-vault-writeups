| Method    | Techniques                                                                     |
| --------- | ------------------------------------------------------------------------------ |
| Manual    | robots.txt, sitemap.xml, favicon fingerprinting, HTTP headers, framework stack |
| OSINT     | Google dorking, Wappalyzer, Wayback Machine, GitHub, S3 buckets                |
| Automated | Gobuster dir, dns, and vhost modes                                             |

## Search Engines & Web Tools
#### Google Dorking
|Filter|Example|Description|
|---|---|---|
|`site`|`site:tryhackme.com`|Returns results only from the specified domain|
|`inurl`|`inurl:admin`|Returns results with the specified word in the URL|
|`filetype`|`filetype:pdf`|Returns results of a specific file type|
|`intitle`|`intitle:admin`|Returns results with the specified word in the page title|
|`intext`|`intext:password`|Returns results containing the specified word in the body|
|`cache`|`cache:tryhackme.com`|Shows Google's cached version of the page|
#### Wappalyzer
Browser extension and online tool that identifies the technologies a website uses, frameworks, CMS platforms, CDNs, analytics tools, payment processors, and more

## Repositories & Archives
#### Wayback Machine
Archive of the internet dating back to late 1990s.
#### Github
#### S3 Buckets
{name}.s3.amazonaws.com.

Common naming patterns include `{company}-assets`, `{company}-backup`, `{company}-www`, and `{company}-dev`. Try these patterns against your target's company name. You can also find bucket URLs in the website's page source or in GitHub repositories.