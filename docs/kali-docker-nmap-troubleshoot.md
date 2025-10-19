The first time trying to use nmap on my Kali Docker container, I received the following error: 

```
/usr/bin/nmap: 6: exec: /usr/lib/nmap/nmap: Operation not permitted
```

A quick Google search returns some results where this seems to be a common issue with Docker containers not having NET_ADMIN capabilities. The most helpful article I found was <a href="https://bugs.kali.org/view.php?id=9085"> this one </a> on the Kali Linux bug tracker. 

It seems this is by design, as the expected use-case is to use Docker containers as privileged or with the specific capabilities needed. 

Long story short, Kali Linux's post install script sets cap_net_admin, while the Docker container does not allow this; there's a permissions mismatch between Kali and Docker. The recommended fix is 

```
etcap cap_net_raw,cap_net_bind_service+eip /usr/lib/nmap/nmap
```
This changes the capabilities on the nmap binary and removes cap_net_admin to align it with the same permissions as the Docker container. 

```
nmap -v
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-19 02:49 UTC
Read data files from: /usr/share/nmap
WARNING: No targets were specified, so 0 hosts scanned.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.12 seconds
           Raw packets sent: 0 (0B) | Rcvd: 0 (0B)
```
We're off to the races! 🏎️ Let's go scan some networks!
