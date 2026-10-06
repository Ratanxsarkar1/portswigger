# Information Disclosure Theory

![Information Disclosure](Attachments/Pasted%20image%2020260903221141.png)

## Overview

Information disclosure occurs when a website unintentionally reveals sensitive information to its users.

## Common Information Disclosure Sources

- [Files for web crawlers](https://portswigger.net/web-security/information-disclosure/exploiting#files-for-web-crawlers)
- [Directory listings](https://portswigger.net/web-security/information-disclosure/exploiting#directory-listings)
- [Developer comments](https://portswigger.net/web-security/information-disclosure/exploiting#developer-comments)
- [Error messages](https://portswigger.net/web-security/information-disclosure/exploiting#error-messages)
- [Debugging data](https://portswigger.net/web-security/information-disclosure/exploiting#debugging-data)
- [User account pages](https://portswigger.net/web-security/information-disclosure/exploiting#user-account-pages)
- [Backup files](https://portswigger.net/web-security/information-disclosure/exploiting#source-code-disclosure-via-backup-files)
- [Insecure configuration](https://portswigger.net/web-security/information-disclosure/exploiting#information-disclosure-due-to-insecure-configuration)
- [Version control history](https://portswigger.net/web-security/information-disclosure/exploiting#version-control-history)

## Exploitation Vectors

### Source Code Disclosure via Backup Files

Text editors often generate temporary backup files while the original file is being edited. These temporary files are usually indicated in some way, such as by appending a tilde (`~`) to the filename or adding a different file extension.

### Information Disclosure Due to Insecure Configuration

The HTTP `TRACE` method is designed for diagnostic purposes and can reveal information about the request as received by intermediate proxies or servers.

### Version Control History

Sensitive repository files and history may be exposed via directories such as:

```text
/.git
```
