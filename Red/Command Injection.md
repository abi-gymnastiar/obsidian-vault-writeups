## Discovery
### PHP
`exec()`, `system()`, `shell_exec()`, and `passthru()`

```php
<?php
$songs = "/var/www/html/songs";                                    // 1

if (isset($_GET["title"])) {
    $title = $_GET["title"];                                       // 2

    $command = "grep $title /var/www/html/songtitle.txt";          // 3

    $search = exec($command);                                      // 4
    if ($search == "") {
        $return = "<p>The requested song</p><p> $title does </p><b>not</b><p> exist!</p>";
    } else {
        $return = "<p>The requested song</p><p> $title does </p><b>exist!</b>";
    }

    echo $return;
}
?>
```

Exploit with `?title=; cat /etc/passwd` to resulted in 

```bash
grep ; cat /etc/passwd /var/www/html/songtitle.txt
```

### Python
`subprocess`

```python
import subprocess
from flask import Flask                                            # 1
app = Flask(__name__)

def execute_command(shell):                                        # 2
    return subprocess.Popen(shell, shell=True, stdout=subprocess.PIPE).stdout.read()

@app.route('/<shell>')                                             # 3
def command_server(shell):
    return execute_command(shell)
```

### Nodejs
`child_process.exec()`

## Useful Payloads
| Payload  | Description                                                                                                                                                                 |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `whoami` | Displays what user the application is running as.                                                                                                                           |
| `ls`     | Lists the contents of the current directory. Configuration files, environment files containing tokens or API keys, and other sensitive data may be present.                 |
| `ping`   | Causes the application to hang for a measurable period. Useful for confirming blind command injection.                                                                      |
| `sleep`  | Another time-based payload for blind injection testing, useful when `ping` is not installed on the target.                                                                  |
| `nc`     | Netcat can be used to spawn a reverse shell on the vulnerable application, providing an interactive foothold to explore the system and look for privilege escalation paths. |

| Payload   | Description                                                                                                                                                 |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `whoami`  | Displays what user the application is running as.                                                                                                           |
| `dir`     | Lists the contents of the current directory. Configuration files, environment files containing tokens or API keys, and other sensitive data may be present. |
| `ping`    | Causes the application to hang for a measurable period. Useful for confirming blind command injection.                                                      |
| `timeout` | Another time-based payload for blind injection testing, useful when `ping` is not installed on the target.                                                  |

## Cheat Sheet
https://github.com/payload-box/command-injection-payload-list

## Remediation
- Careful with vulnerable functions like exec(), passthru(), and system()
- Input sanitisation.
- Understand that filters are not always bulletproof.