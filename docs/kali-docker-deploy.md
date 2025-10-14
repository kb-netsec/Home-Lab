This document is a record of my process for deploying Kali Linux via a Docker Image for pen-testing labs. I'm preferring Docker Images due to my compute, time, and budget constraints. This allows me to quickly deploy a lab-ready attack machine with low overhead and no additional cost. 

Install docker:

    sudo apt docker.io 
    sudo apt docker-compose

Turn that bad larry on: 

    sudo systemctl enable --now docker

Validate Docker install. The --rm flag closes the container on exit. Pretty handy. Note all my docker commands use sudo because I refuse to add my user to the docker group. Container privilege escalation just sounds bad.

    sudo docker run --rm hello-world
    sudo docker version

That was easy. I picked up a couple of tips along the way.

    #To list running containers:
    sudo docker ps -a 

    #Example:
    sudo docker ps -a
    CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
    dc2419981bad   hello-world   "/hello"   18 seconds ago   Exited (0) 16 seconds ago             goofy_mendeleev

    #Stop containers:
    sudo docker rm dc2419981bad
    sudo docker ps -a 
    CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES

Now let's get cooking

    #Download the Kali rolling image from Docker Hub 
    sudo docker pull docker.io/kalilinux/kali-rolling 

Let's check the size

    sudo docker image ls
    REPOSITORY               TAG       IMAGE ID       CREATED        SIZE
    kalilinux/kali-rolling   latest    5d4447aae541   5 days ago     124MB

Hah! 124 megs. That beats the pants off of even a live boot USB, and especially a VM. Mind, this is headless and without tools by default, but I'm going for terminal profifiency and potato compute here. I can't complain.  We'll see how big it is after installing tools. 


Here goes nothin'

    docker run --tty --interactive kalilinux/kali-rolling

Well I'll be darned, I followed the documentation and it worked. 

Now to get some tools installed. Per the documentation on <a href="https://hub.docker.com/r/kalilinux/kali-rolling">Docker Hub</a>, I'll go with 

    apt update && apt -y install kali-linux-headless

While it didn't take too long to download all the packages, there were a few prompts regarding certain packages like Kismet, Wireshark, etc. that took some careful reading. Unpacking packages took forever. I went to sleep. 

Here are the prompts and my selections:

    Configuring keyboard-configuration
    Please select the layout matching the keyboard for this machine.
    1. English (US)
    2. English (US) - Cherokee
    3. English (US) - English (Colemak)
    4. English (US) - English (Colemak-DH ISO)
    5. English (US) - English (Colemak-DH Ortholinear)
    6. English (US) - English (Colemak-DH Wide ISO)
    7. English (US) - English (Colemak-DH Wide)
    8. English (US) - English (Colemak-DH)
    9. English (US) - English (Dvorak)
    10. English (US) - English (Dvorak, Macintosh)
    11. English (US) - English (Dvorak, alt. intl.)
    12. English (US) - English (Dvorak, intl., with dead keys)
    13. English (US) - English (Dvorak, left-handed)
    14. English (US) - English (Dvorak, right-handed)
    15. English (US) - English (Macintosh)
    16. English (US) - English (Norman)
    17. English (US) - English (US, Symbolic)
    18. English (US) - English (US, alt. intl.)
    19. English (US) - English (US, euro on 5)
    20. English (US) - English (US, intl., with dead keys)
    21. English (US) - English (Workman)
    22. English (US) - English (Workman, intl., with dead keys)
    23. English (US) - English (classic Dvorak)
    24. English (US) - English (intl., with AltGr dead keys)
    25. English (US) - English (programmer Dvorak)
    26. English (US) - English (the divide/multiply toggle the layout)
    27. English (US) - Hawaiian
    28. English (US) - Russian (US, phonetic)
    29. English (US) - Serbo-Croatian (US)
    30. Other
    Keyboard layout: 
    Keyboard layout: 1

    Configuring console-setup-

    Please choose the character set that should be supported by the console font.

    If you don't use a framebuffer, the choices that start with "." will reduce the number of available colors on the
    console.

    1. . Arabic
    2. # Armenian
    3. # Cyrillic - KOI8-R and KOI8-U
    4. # Cyrillic - non-Slavic languages
    5. . Cyrillic - non-Slavic languages (for blind users)
    6. # Cyrillic - Slavic languages (also Bosnian and Serbian Latin)
    7. . Cyrillic - Slavic languages (for blind users)
    8. . Ethiopic
    9. # Georgian
    10. # Greek
    11. . Greek (for blind users)
    12. # Hebrew
    13. # Lao
    14. # Latin1 and Latin5 - western Europe and Turkic languages
    15. # Latin2 - central Europe and Romanian
    16. # Latin3 and Latin8 - Chichewa; Esperanto; Irish; Maltese and Welsh
    17. # Latin7 - Lithuanian; Latvian; Maori and Marshallese
    18. . Latin - Vietnamese
    19. # Thai
    20. . Combined - Latin; Slavic Cyrillic; Hebrew; basic Arabic
    21. . Combined - Latin; Slavic Cyrillic; Greek
    22. . Combined - Latin; Slavic and non-Slavic Cyrillic
    23. Guess optimal character set
    Character set to support: 23

    Configuring kismet-capture-common

    Kismet needs root privileges for some of its functions. However, running it as root ("sudo kismet") is not
    recommended, since running all of the code with elevated privileges increases the risk of bugs doing system-wide
    damage. Instead Kismet can be installed with the "setuid" bit set, which will allow it to grant these privileges
    automatically to the processes that need them, excluding the user interface and packet decoding parts.

    Enabling this feature allows users in the "kismet" group to run Kismet (and capture packets, change wireless card
    state, etc), so only thoroughly trusted users should be granted membership of the group.

    For more detailed information, see the Kismet 010-suid.md, which can be found at
    "/usr/share/doc/kismet-doc/readme/010-suid.md" in kismet-doc package or
    "https://www.kismetwireless.net/docs/readme/suid/".

    Install Kismet "setuid root"? [yes/no] yes

    Only users in the kismet group are able to use kismet under the setuid model.

    Please specify the users to be added to the group, as a space-separated list.

    Note that currently logged-in users who are added to a group will typically need to log out and log in again before
    it is recognized.

    Users to add to the kismet group: korey

    The provided user list contains invalid usernames.

    The users to be added to the kismet group have to be provided in a space-separated list of usernames. It seems that
    the following usernames are not valid: korey. Please revise the list.

    Only users in the kismet group are able to use kismet under the setuid model.

    Please specify the users to be added to the group, as a space-separated list.

    Note that currently logged-in users who are added to a group will typically need to log out and log in again before
    it is recognized.

    Users to add to the kismet group: root

    Configuring macchanger


    Please specify whether macchanger should be set up to run automatically every time a network device is brought up or
    down. This gives a new MAC address whenever you attach an ethernet cable or reenable wifi.

    Change MAC automatically? [yes/no] y

    Configuring wireshark-common


    Dumpcap can be installed in a way that allows members of the "wireshark" system group to capture packets. This is
    recommended over the alternative of running Wireshark/Tshark directly as root, because less of the code will run
    with elevated privileges.

    For more detailed information please see /usr/share/doc/wireshark-common/README.Debian.gz once the package is
    installed.

    Enabling this feature may be a security risk, so it is disabled by default. If in doubt, it is suggested to leave it
    disabled.

    Should non-superusers be able to capture packets? [yes/no] yes

    sslh configuration


    sslh can be run either as a service from inetd, or as a standalone server. Each choice has its own benefits. With
    only a few connection per day, it is probably better to run sslh from inetd in order to save resources.

    On the other hand, with many connections, sslh should run as a standalone server to avoid spawning a new process for
    each incoming connection.

    1. from inetd  2. standalone
    Run sslh: 1


I eventually needed to return to real life. After a reboot, I needed to get back into my Kali container.

    #Find the container ID.
    sudo docker ps -a
    CONTAINER ID   IMAGE                    COMMAND   CREATED        STATUS                      PORTS     NAMES
    81b61fb11643   kalilinux/kali-rolling   "bash"    46 hours ago   Exited (255) 23 hours ago             pedantic_banzai
    #Now need to start the container. It was stopped after a reboot.
    sudo docker start 81b61fb11643
    #Now let's jump back in
    sudo docker attach 81b61fb11643

Now I have a working Kali container on my system which I can use to pentest other VMs in my home lab.
