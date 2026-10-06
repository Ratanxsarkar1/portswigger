# Path Traversal Theory

On Windows, both `../` and `..\` are valid directory traversal sequences.

## Common Obstacles to Exploiting Path Traversal Vulnerabilities

Applications often implement defenses that strip or block directory traversal sequences from the user-supplied filename.

### Absolute Path Bypass

Use an absolute path from the filesystem root directly:

```text
filename=/etc/passwd
```

### Nested Traversal Sequences

Nested traversal sequences, such as `....//` or `....\/`. These revert to simple traversal sequences when the inner sequence is stripped non-recursively.

### URL Encoding

Encoding traversal sequences (e.g., in the `filename` parameter of a `multipart/form-data` request):

```text
../ > %2e%2e%2f > %252e%252e%252f
```

### Validation of Start of Path

When the application requires the path to start with the expected base folder:

```text
filename=/var/www/images/../../../etc/passwd
```

### Null Byte Bypass

When the application validates the file extension, bypass using a null byte:

```text
%00
```
