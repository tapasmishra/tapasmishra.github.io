---
title: How to install Docker on Raspberry Pi
header:
  image: /assets/images/docker.jpg
  teaser: /assets/images/docker.jpg
date: 2020-05-17 13:35:20 +0200
tags: 
  - Docker
  - Linux
categories: 
  - docker
---

### Installing Docker on Raspberry Pi

To install Docker on our Reaspberry Pi first we need to SSH to the instance and then we have to switch to `root` user to proceed with installation. 

{% highlight js %}
ssh pi@192.168.1.131
pi@192.168.1.131's password:
Linux raspberrypi 4.19.97-v7l+ #1294 SMP Thu Jan 30 13:21:14 GMT 2020 armv7l

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Mar 31 08:21:02 2020
pi@raspberrypi:~ $ sudo su
root@raspberrypi:/home/pi# cd
{% endhighlight %}

The below mentioned signle liner command is sufficient to install Docker on Raspberry PI

`` curl -sSL https://get.docker.com/ | sh ``

The above single liner command will install Docker as well as it's dependency packages:

{% highlight js %}
root@raspberrypi:~# curl -sSL https://get.docker.com/ | sh
# Executing docker install script, commit: 26ff363bcf3b3f5a00498ac43694bf1c7d9ce16c
+ sh -c apt-get update -qq >/dev/null
+ sh -c DEBIAN_FRONTEND=noninteractive apt-get install -y -qq apt-transport-https ca-certificates curl >/dev/null
+ sh -c curl -fsSL "https://download.docker.com/linux/raspbian/gpg" | apt-key add -qq - >/dev/null
Warning: apt-key output should not be parsed (stdout is not a terminal)
+ sh -c echo "deb [arch=armhf] https://download.docker.com/linux/raspbian buster stable" > /etc/apt/sources.list.d/docker.list
+ sh -c apt-get update -qq >/dev/null
+ [ -n  ]
+ sh -c apt-get install -y -qq --no-install-recommends docker-ce >/dev/null
+ sh -c docker version
Client: Docker Engine - Community
 Version:           19.03.8
 API version:       1.40
 Go version:        go1.12.17
 Git commit:        afacb8b
 Built:             Wed Mar 11 01:35:24 2020
 OS/Arch:           linux/arm
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.8
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.17
  Git commit:       afacb8b
  Built:            Wed Mar 11 01:29:22 2020
  OS/Arch:          linux/arm
  Experimental:     false
 containerd:
  Version:          1.2.13
  GitCommit:        7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
If you would like to use Docker as a non-root user, you should now consider
adding your user to the "docker" group with something like:

  sudo usermod -aG docker your-user

Remember that you will have to log out and back in for this to take effect!

WARNING: Adding a user to the "docker" group will grant the ability to run
         containers which can be used to obtain root privileges on the
         docker host.
         Refer to https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface
         for more information.
root@raspberrypi:~#
{% endhighlight %}

### Verify Docker version

{% highlight js %}
root@raspberrypi:~# docker version
Client: Docker Engine - Community
 Version:           19.03.8
 API version:       1.40
 Go version:        go1.12.17
 Git commit:        afacb8b
 Built:             Wed Mar 11 01:35:24 2020
 OS/Arch:           linux/arm
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.8
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.17
  Git commit:       afacb8b
  Built:            Wed Mar 11 01:29:22 2020
  OS/Arch:          linux/arm
  Experimental:     false
 containerd:
  Version:          1.2.13
  GitCommit:        7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
root@raspberrypi:~#
{% endhighlight %}

Now we have installed Docker `19.03` on our Rasphberry Pi. But if you careffully read the out put of our installation command; you should able to see that it's instructed to add the non-root user's to the docker group if you like to use Docker it as non-root user.

In our case we have to add `pi` user to the `docker` group. Below is the command that we nee to execute to add the user `pi` to `docker` group and verify:

{% highlight js %}
root@raspberrypi:~# usermod -aG docker pi
root@raspberrypi:~# cat /etc/group | grep docker
docker:x:995:pi
root@raspberrypi:~#
{% endhighlight %}

Let us switch to the `pi` user and see if we are able to run any `docker` command or not

### Run hello-world container

Using the below command we will run the `hello-world` container.

```javascript
root@raspberrypi:~# su - pi
pi@raspberrypi:~ $ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
4ee5c797bcd7: Pull complete
Digest: sha256:6a65f928fb91fcfbc963f7aa6d57c8eeb426ad9a20c7ee045538ef34847f44f1
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm32v7)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

pi@raspberrypi:~ $
```

In next chapter we will install / configure [`Docker Swarm Cluster`](https://www.linuxfunda.com/Docker-Swarm-Cluster-on-Raspberry-Pi/). 
