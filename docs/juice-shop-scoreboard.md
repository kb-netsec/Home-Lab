This documentation will serve as a process for my introduction to web application pentesting. I'm going to make
a lot of mistakes and go down a lot of rabbit holes, and it'll be fun! 

I'm going to try to use as little hints, walkthroughs, and "cheats" as possible. My first task is to find the 
scoreboard. 

I started reading about the scoreboard in Pwning OWASP Juice Shop by the developer. I didn't want to spoil any
hints though. I did use the "help getting started" button to get some helpful hints about finding the 
scoreboard, which were guessing the URL or using client-side JavaScript in deve tools. 

I started with some basic URL manipulation and tried the following:

http://localhost:3000/scoreboard
http://localhost:3000/score
http://localhost:3000/board 
http://localhost:3000/#/scoreboard
http://localhost:3000/#/score
http://localhost:3000/#/board

Having no luck, I decided to try dev tools. 

I go to the sources tab, and take a look at the main.js code. Now I'm a complete JavaScript (and coding) noob,
but my lazy instincts take over here. I press Ctrl + F and type in scoreboard. I see 19 hits.

I'm greeted first with the following line: 

```
scoreBoardVisible = !1;
```

I'm thinking maybe the ! is commenting out the value of 1 for true. I'll come back to this later. After perusing
through the remaining matching hits, I see lots of references to the scoreboard and what seems to be JS code 
related to certain scoreboard statuses. 

Let's see what happens if we remove the !. I do so and check the hamburger menu. No change. I scroll through
the products in the shop just to make sure I'm not missing anything. Nada. How about if I change it to a zero? 
I do. No change. 

Moving on, the next hit shows the following:

```
  this.getScoreBoardStatus(),
                    this.ngZone.runOutsideAngular( () => {
                        this.io.socket().on("challenge solved", e => {
                            "scoreBoardChallenge" === e.key && (this.scoreBoardVisible = !0)
```

I interpret this to mean that if the scoreBoardChallenge is complete AND the scoreBoardVisible value is set 
correctly, you'll be able to get the score board status. It looks like the value should be !0. Haven't tried
that yet. 

I change the line to 

```
scoreBoardVisible = !0;
```

However, I'm still not seeing any change. Wonder if there's something I need to do to make it stick. Feeling 
frustrated, I do a keyword search in vendor.js and polyfills.js as well. No hits on score, scoreboard, and
board only returns hits on keyboard in vendor.js. 


