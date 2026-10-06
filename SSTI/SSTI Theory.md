# Server-Side Template Injection (SSTI) Theory

Reference: [SSTI Video Tutorial](https://youtu.be/3cT0uE7Y87s?si=8Ux60fE5toZxA0SB)

## Overview

Server-side template injection vulnerabilities arise when user input is concatenated into templates rather than being passed in as data.

### Vulnerability Example (Twig)

- **Secure (not vulnerable):** Passing user input safely as template data:
  ```php
  $output = $twig->render("Dear {first_name},", array("first_name" => $user.first_name));
  ```

- **Vulnerable:** Concatenating user input directly into the template string:
  ```php
  $output = $twig->render("Dear " . $_GET['name']);
  ```

## Template Injection Methodology

### Constructing a Server-Side Template Injection Attack

![SSTI Methodology](Attachments/Pasted%20image%2020260819152235.png)

#### Step 1: Detect

Inject a fuzz string containing common template characters:

```text
${{<%[%'"}}%\
```

- If an exception is raised, this indicates that the injected template syntax is potentially being interpreted by the server in some way.
- Submitting invalid syntax is often enough because the resulting error message will tell you exactly what the template engine is.

#### Step 2: Identify

Use syntax probes to identify the specific template engine:

![Template Engine Decision Tree](Attachments/Pasted%20image%2020260819152436.png)

> **Note:** Green and red arrows represent 'success' and 'failure' responses respectively.

Mathematical evaluation proof of concept:

```http
http://vulnerable-website.com/?username=${7*7}
```

If the resulting output contains `Hello 49`, this shows that the mathematical operation is being evaluated server-side. This is a good proof of concept for a server-side template injection vulnerability.

### Environment Variable Disclosure

List all variables in the environment using the following injection in Java-based templating languages:

```text
${T(java.lang.System).getenv()}
```
