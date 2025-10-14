This'll be a quick one. From <a href="https://hub.docker.com/r/bkimminich/juice-shop#setup">Docker Hub</a>:

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

I try something else, running the same command as above with port 3001 instead. I notice the container checks for port 3000 to be available. It's very likely not going to work on 3001. Let's try running it with the --rm flag. 

Interesting. I'm not getting the confirmation that the server is listening on port 3000 anymore. I didn't make any changes with the exception of stopping and removing the container and adding the --rm flag. 

  

