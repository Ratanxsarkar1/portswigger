# Access Control Vulnerabilities Theory

## Core Concepts

- **Authentication** confirms that the user is who they say they are.
- **Session management** identifies which subsequent HTTP requests are being made by that same user.
- **Access control** determines whether the user is allowed to carry out the action that they are attempting to perform.

![Access Control Concepts](Attachments/Pasted%20image%2020260817134211.png)

## Parameter-Based Access Control Methods

Some applications determine user access rights or roles using parameter values submitted in requests:
- A hidden field.
- A cookie.
- A preset query string parameter.

## Broken Access Control Resulting from Platform Misconfiguration

Access controls may be enforced at the web server or proxy level based on URL and HTTP method rules, for example:

```text
DENY: POST, /admin/deleteUser, managers
```

Such controls can often be bypassed through:

- **Non-standard HTTP headers**: Using headers like `X-Original-URL` and `X-Rewrite-URL` to override the request URL path on systems configured with reverse proxies.
- **Method switching**: Using the `GET` (or another) method to perform actions on a restricted URL when controls only match specific HTTP methods.
- **URL case tolerance**: A request to `/ADMIN/DELETEUSER` may still be mapped to the `/admin/deleteUser` endpoint by the framework while bypassing case-sensitive filter rules.
- **Spring Framework suffix pattern matching**:
  When `useSuffixPatternMatch` is enabled, paths with an arbitrary file extension are mapped to an equivalent endpoint with no file extension (prior to Spring 5.3, this option was enabled by default).
- **Path normalization discrepancies**: Discrepancies in whether `/admin/deleteUser` and `/admin/deleteUser/` are treated as distinct endpoints by intermediate filters vs. backend handlers.
- **Data leakage in redirects**: The application detects an unauthorized request and returns a redirect to the login page. However, the response body containing the redirect might still include sensitive data belonging to the targeted user.

## Insecure Direct Object References (IDOR)

Occurs when an application provides direct access to objects based on user-supplied input without proper access control checks.

## Access Control Vulnerabilities in Multi-Step Processes

In multi-step workflows, access controls are sometimes enforced only on initial steps but omitted on subsequent confirmation steps:

1. Load the form that contains details for a specific user.
2. Submit the changes.
3. Review the changes and confirm.

## Referer-Based Access Control

Some applications enforce access control by checking the HTTP `Referer` header (e.g., verifying that requests originated from the main administrative page at `/admin`, but failing to protect sub-pages such as `/admin/deleteUser`).
