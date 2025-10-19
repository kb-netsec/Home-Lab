The first time trying to use nmap on my Kali Docker container, I received the following error: 

```
/usr/bin/nmap: 6: exec: /usr/lib/nmap/nmap: Operation not permitted
```

A quick Google search returns some results where this seems to be a common issue with Docker containers not having NET_ADMIN capabilities. The most helpful article I found was <a href="https://bugs.kali.org/view.php?id=9085" this one </a> 
