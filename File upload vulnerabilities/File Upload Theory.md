# File Upload Vulnerabilities Theory

![File Upload Vulnerabilities](Attachments/Pasted%20image%2020260822141213.png)

## Overview

The `Content-Type` response header may provide clues as to what kind of file the server thinks it has served.

## Web Shells

A web shell is a malicious script that enables an attacker to execute arbitrary commands on a remote web server simply by sending HTTP requests to the right endpoint.

A versatile web shell may look something like this:

```php
<?php echo system($_GET['command']); ?>
```

This script enables you to pass an arbitrary system command via a query parameter as follows:

```http
GET /example/exploit.php?command=id HTTP/1.1
```

## Preventing File Execution in User-Accessible Directories

Web servers often attempt to prevent script execution inside upload directories by configuring those paths to serve files only as static resources or disabling execution handlers.

> **Related Lab:** Lab: Web shell upload via path traversal

---

## Insufficient Blacklisting of Dangerous File Types

Applications often attempt to blacklist common extensions like `.php`, but fail to block alternative executable extensions:
- `.php5`, `.shtml`, and so on.

Web servers may also support special directory-level configuration files:
- **Apache:** `.htaccess`
- **IIS:** `web.config`

### Apache Global Configuration

On Ubuntu/Debian, Apache's main configuration is located at:

```text
/etc/apache2/apache2.conf
```

The configuration is organized roughly like:

```text
/etc/apache2/
├── apache2.conf          ← main/global configuration
├── ports.conf
├── sites-available/
│   └── 000-default.conf
├── sites-enabled/
│   └── 000-default.conf → ../sites-available/000-default.conf
├── mods-available/
├── mods-enabled/
├── conf-available/
└── conf-enabled/
```

### Per-Directory Overrides (.htaccess)

`.htaccess` is a **per-directory Apache configuration file**.

For example:

```text
/var/www/html/
├── index.php
├── .htaccess
└── uploads/
    ├── image.jpg
    └── .htaccess
```

An `.htaccess` file inside `/var/www/html/` can affect that directory and its subdirectories.

The server must also allow `.htaccess` overrides. This is controlled by Apache's configuration, commonly with directives such as:

```apache
<Directory /var/www/html>
    AllowOverride All
</Directory>
```

If overrides aren't permitted, simply uploading an `.htaccess` file won't make Apache apply its directives.

---

## Obfuscating File Extensions

Techniques to bypass extension filters:
- **Case variations:** `exploit.pHp` is in fact a `.php` file on case-insensitive filesystems.
- **Multiple extensions:** `exploit.php.jpg`
- **Null byte injection:** `exploit.php%00.jpg`
- **Trailing characters:** Ignoring trailing whitespaces, dots, and suchlike: `exploit.php.`
- **URL encoding:** `exploit%2Ephp`
- **Path/semicolon separators (IIS):** `exploit.asp;.jpg` or `exploit.asp%00.jpg`
- **Non-recursive stripping:** `exploit.p.phphp`

---

## Flawed Validation of File Contents

Validating files based purely on magic bytes rather than complete file structure:
- JPEG files always begin with the bytes `FF D8 FF`.
- **Polyglot:** Crafting a valid image file that also contains executable PHP code.

---

## Exploiting File Upload Race Conditions

For example, some websites upload the file directly to the main filesystem and then remove it again if it doesn't pass validation.

- [Turbo Intruder Extension](https://portswigger.net/bappstore/9abaa233088242e8be252cd4ff534988)
- Turbo Intruder is a Burp Suite extension for sending large numbers of HTTP requests and analyzing the results. It's intended to complement Burp Intruder by handling attacks that require extreme speed or complexity.

## Testing Tips

> **Tip:** You can try sending `OPTIONS` requests to different endpoints to test for any that advertise support for the `PUT` method.
