# Server Side Vulnerabilities - Path Traversal
Date: 11/08/2025
URL: https://portswigger.net/web-security/learning-paths/server-side-vulnerabilities-apprentice/path-traversal-apprentice/file-path-traversal/what-is-path-traversal

As part of my introduction to cyber security and pentesting, I'll be learning BurpSuite. I've installed the 
Community Edition and am going through the web security academy.

The first learning path I chose was the server-side vulnerabilities path. 

The first vulnerability I learned about was path traversal, where you can read (or even write!)arbitrary server
files that the developer or admin did not intend to be public. This is done by requesting a URL on an insecure
web server that points to an unintended path. For example, instead of 

```
https://insecure-website.com/loadImage?filename=../../../etc/passwd
```

The first lab presents a shopping website with the objective of retrieving he contents of /etc/passwd. 

I first began intercepting HTTP traffic with Burp's proxy. When a request was made to the server to fetch the product images, it was a simple task of modifying the filename. For example, in the first GET request, turning

```
GET /image?filename=38.jpg HTTP/2
```

Into 

```
GET /image?filename=../../../etc/passwd HTTP/2
```

We can see doing so sends a WebSocket response to the client containing some HTML that modifies the lab banner on the site to show the lab is solved and a congratulatory pop-up. And here I was hoping for the contents of an actual passwd file...

See you in the next write up!

