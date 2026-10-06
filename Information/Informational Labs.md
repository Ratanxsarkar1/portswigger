# Information Disclosure Labs

## Lab: Information disclosure in error messages

### Exploitation

Trigger a runtime error by submitting an unexpected data type:

```text
productId=<garbage>
```

---

## Lab: Information disclosure on debug page

### Analysis

Comment found in page source:

```html
<!-- <a href=/cgi-bin/phpinfo.php>Debug</a> -->
```

---

## Lab: Source code disclosure via backup files

### Exploitation

Check backup directory:

```text
/backup   --> ProductTemplate.java.bak
```

---

## Lab: Authentication bypass via information disclosure

### Analysis

Header identified revealing original client IP header requirement:

```http
X-Custom-IP-Authorization: 152.58.121.60
```

![Authentication bypass via information disclosure](Attachments/Pasted%20image%2020260903224059.png)

### Exploitation

Configure Burp Suite to spoof local access:

1. Go to **Proxy > Match and replace**.
2. Under **HTTP match and replace rules**, click **Add**. The **Add match/replace rule** dialog opens.
3. Leave the **Match** field empty.
4. Under **Type**, make sure that **Request header** is selected.
5. In the **Replace** field, enter the following:

   ```http
   X-Custom-IP-Authorization: 127.0.0.1
   ```

---

## Lab: Information disclosure in version control history

### Exploitation

Download the exposed repository and inspect commit history:

```bash
wget -r location
git log
git show <hash>
```