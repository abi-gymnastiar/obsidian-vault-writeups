| Input                                    | Resulting Server-Side Request                           |
| ---------------------------------------- | ------------------------------------------------------- |
| `server=api`                             | `https://server.website.thm/api/item?id=2`              |
| `server=server.website.thm/flag?id=9&x=` | `https://server.website.thm/flag?id=9&x=/api/item?id=2` |

Append `&x=` at the end to ignore the rest of the URL

## Partial URL (hostname or path only)

```http
https://website.com/stock?server=api.internal
```

Can replace it:

```http
https://website.com/stock?server=attacker.com
```

## Path Traversal

```http
https://website.com/stock?url=/item/123/details
```

Can supply `/../admin`, causing the server to request:

```http
https://website.com/admin
```

## Hidden Form Fields

```html
<input type="hidden" name="avatar" value="/images/avatars/default.png">
```

# Finding an SSRF
## Common Indicators
- Full URL in a parameter
- Hidden form fields.
- Partial URL (hostname only)
- Path only

Other these patterns, some app features frequently contain SSRF vectors:

| Feature               | Why It's Relevant                                                                |
| --------------------- | -------------------------------------------------------------------------------- |
| Webhook configuration | The application makes a request to a user-supplied URL to verify the endpoint.   |
| PDF/report generation | The server fetches content from a supplied URL to render it into a document.     |
| URL preview/unfurling | The application retrieves metadata (title, thumbnail) from a user-provided link. |
| File import by URL    | The server downloads a file from a remote location specified by the user.        |
| Integration settings  |                                                                                  |

## Confirming Blind SSRF

| Method                                          | How It Works                                                                                                                                                                               |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| External HTTP logger (e.g. requestbin.com)      | Supply the logger's URL as the SSRF payload. Check the dashboard for incoming requests from the target server.                                                                             |
| Burp Collaborator                               | Generates a unique domain that logs HTTP and DNS callbacks. Useful when HTTP is blocked but DNS resolution still occurs.                                                                   |
| Self-hosted listener (`python3 -m http.server`) | Run a simple HTTP server on your own machine and monitor for incoming connections from the target.                                                                                         |
| Timing analysis                                 | Compare response times for requests to internal hosts that exist versus hosts that do not. Consistent differences indicate the server is resolving and connecting to the supplied address. |
| Error-based inference                           | Different error messages for reachable versus unreachable hosts reveal information about the internal network, even when the actual response body is hidden.                               |

# Defenses Against Common SSRF
## Deny Lists (not optimal)

|Representation|Value|
|---|---|
|Standard|`127.0.0.1`|
|Decimal|`2130706433`|
|Octal|`017700000001`|
|Shorthand|`127.1` or `0` or `0.0.0.0`|
|Wildcard|`127.*.*.*`|
|IPv6|`[::1]`|
|DNS-based|`127.0.0.1.nip.io`|

## Allow Lists (more optimal)

| Bypass Technique     | Example                                    |
| -------------------- | ------------------------------------------ |
| Subdomain matching   | `https://website.com.attackers-domain.thm` |
| URL credential abuse | `https://website.com@attacker.com/`        |

## Open Redirects
A method for attacker to bypass strict rules. An endpoint that forwards visitors to a URL specified in a parameter. These are commonly used for tracking outbound link clicks.

```http
https://website.com/link?url=https://differentweb.com
```

open redirect:

```http
https://website.com/link?url=http://169.254.169.254/latest/meta-data/
```

