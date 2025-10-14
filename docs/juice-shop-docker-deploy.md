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





