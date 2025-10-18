In this document I'm going to be covering the deployment of Juice Shop in home lab. Note that sudo has been included in all docker commands, as I'm not giving carte blanche to root on my system. See the <a href="https://docs.docker.com/engine/security/#docker-daemon-attack-surface">Docker Daemon Attack Surface</a> article for details.

From the <a href="https://hub.docker.com/r/bkimminich/juice-shop#setup">Docker Hub juice shop page</a>:

```
sudo docker pull bkimminich/juice-shop
sudo docker run --rm -p 3000:3000 bkimminich/juice-shop
info: Detected Node.js version v22.18.0 (OK)
info: Detected OS linux (OK)
info: Detected CPU x64 (OK)
info: Configuration default validated (OK)
info: Entity models 20 of 20 are initialized (OK)
info: Required file server.js is present (OK)
info: Required file tutorial.js is present (OK)
info: Required file index.html is present (OK)
info: Required file styles.css is present (OK)
info: Required file main.js is present (OK)
info: Required file runtime.js is present (OK)
info: Required file vendor.js is present (OK)
info: Port 3000 is available (OK)
info: Chatbot training data botDefaultTrainingData.json validated (OK)
info: Domain https://www.alchemy.com/ is reachable (OK)

info: Server listening on port 3000
```
I opted not to run the container with the --rm flag as I'd like it to persist while I explore it for the first time. When I try to access the server, I get "This site can't be reached". Ok, what's wrong and what happened?

I try something else, running the same command as above with port 3001 instead. I notice Juice Shop checks for port 3000 to be available during startup validations. It's very likely not going to work on 3001. Let's try running it with the --rm flag. Still no joy.


Consulting the <a href="https://pwning.owasp-juice.shop/companion-guide/latest/part1/running.html">source's</a> documentation, we see a different command for running the Docker container:

```
sudo docker run -d -p 127.0.0.1:3000:3000 bkimminich/juice-shop
a11f63a8097cc203b98b879de423ba3cfce638f16a44ffbd88dc5d6fa6b700dc
```

I try this and observe that this time the app doesn't even begin startup validations. Instead, I get a bunch of seemingly gibberish. It does appear however that teh container is at least running. 

```
sudo docker ps -a
CONTAINER ID   IMAGE                    COMMAND                  CREATED              STATUS                     PORTS                      NAMES
a11f63a8097c   bkimminich/juice-shop    "/nodejs/bin/node /j…"   About a minute ago   Up About a minute          127.0.0.1:3000->3000/tcp   affectionate_noyce
```
Still no joy when navigating to http://localhost:3000.

At this point, I remove the Juice Shop image.

```
sudo docker rmi <image-id>
```

I follow the process above to download the image again. Like before, the server refuses to listen on port 3000. I decide to see if this port is being blocked. First, let's see if there are even any services listening on 3000.

```
netstat -tulpen | grep "3000"
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 0.0.0.0:3000            0.0.0.0:*               LISTEN      0          81299      -                   
tcp6       0      0 :::3000                 :::*                    LISTEN      0          81300      -
```

Ok, there are services listening on 3000. 

At this point, I begin to suspect there's something wrong with the Juice Shop container. I decide to remove it and download it again:

```
#Check ID of juice-shop image
sudo docker images
REPOSITORY               TAG       IMAGE ID       CREATED        SIZE
kalilinux/kali-rolling   latest    5d4447aae541   9 days ago     124MB
bkimminich/juice-shop    latest    df3c7e65b133   5 weeks ago    421MB
hello-world              latest    1b44b5a3e06a   2 months ago   10.1kB

#Remove juice-shop image
sudo docker rmi df3c7e65b133

#Download juice-shop again
sudo docker pull bkimminich/juice-shop

#Run juice-shop
sudo docker run --rm -p 3000:3000 bkimminich/juice-shop
```

I open a browser and navigate to http://localhost:3000 and...success! 🎉

I'm now running JS from my Docker container. This allows me to practice exploiting vulnerable modern web apps in a lightweight lab all from my laptop!





