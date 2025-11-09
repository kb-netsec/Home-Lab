As part of my introduction to cyber security and pentesting, I'll be learning BurpSuite. I've installed the 
Community Edition and am going through the web security academy. 

The first learning path I chose was the server-side vulnerabilities path. 

The first vulnerability I learned about was path traversal, where you can read (or even write!)arbitrary server
files that the developer or admin did not intend to be public. This is done by requesting a URL on an insecure
web server that points to an unintended path. For example, instead of 

```
https://insecure-website.com/loadImage?filename=../../../etc/passwd
```



