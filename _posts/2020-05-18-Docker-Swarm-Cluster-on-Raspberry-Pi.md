---
title: Setup Docker Swarm Cluster on Raspberry Pi
date: 2020-05-18 13:35:20 +0200
tags: 
  - Docker 
  - Linux 
  - Swarm
  - Cluster
categories: 
  - docker
---

In the [previous chapter](https://www.linuxfunda.com/how-to-install-docker-on-raspberry-pi/) we learnt how to install Docker engine. Now in this chapter we will learn how to install Swarm mode. 

First upall tet us setup a 1 node Sawrm Cluster. We will see how Swarm mode works and we will understand few components of it. Then in our next chapter we will add 2 more nodes to our cluster. Asuming that we have already installed Docker Engine on our instance I will directly start from configuring the Swarm Mode. 

To create the Swarm you need to first SSH to the host machine whre the Docker engine is installed. In my case I will SSH to the maching which IP is `192.168.0.131`

```shellscript

Using username "pi".
pi@192.168.0.131's password:
Linux raspberrypi 4.19.97-v7l+ #1294 SMP Thu Jan 30 13:21:14 GMT 2020 armv7l

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon May 18 19:08:22 2020 from 192.168.0.102
pi@raspberrypi:~ $

```

Run the following command to start a new swarm:

```shellscript
pi@raspberrypi:~ $ docker swarm init
Swarm initialized: current node (jdu8ge8rafvdsiu9x9nhvhymp) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-0490adm0y5alpu48ydophj3b4lms61bkgofa0l9rcvuar0iyjf-4wturco6v3tddcq1fvtgt8v8a 192.168.0.131:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

pi@raspberrypi:~ $
```

> If you have more than one IP attached to your machine then you need to specify `--advertise-addr`

Run the below mentioned `node ls` command to list all available nodes.

```shellscript
pi@raspberrypi:~ $ docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
jdu8ge8rafvdsiu9x9nhvhymp *   raspberrypi         Ready               Active              Leader              19.03.8
pi@raspberrypi:~ $
```

Now you can see that we have one node which is basically a `Leader` is activie and it's stastus is ready. On the machine we have installed the Docker engine and on top of that we have created a Swarm mode. Now we can either choose the Docker engine to run our container or we can run our container inside our Docker Swarm. Lets start and run a container inside our swarm.
To run a container inside the swarm normal `docker run` command won't work. This will start the container in the Docker engine itself. To start a container inside the swarm we have use the command `docker service`.

Run the command mentioned below to start/ run a nginx container inside swarm as `web` service. 

```shellscript
pi@raspberrypi:~ $ docker service create --name web --publish 8080:80 nginx
htlom33no80y56f5zcyqwx6ak
overall progress: 1 out of 1 tasks
1/1: running   [==================================================>]
verify: Service converged
pi@raspberrypi:~ $
```
Here we are asking docker to create a service and name it as `web`. Publish the container port 80 to host port 8080. Use `nginx` image for the container. Now run the bellow command to list our serices.

```shellscript
pi@raspberrypi:~ $ docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
htlom33no80y        web                 replicated          1/1                 nginx:latest        *:8080->80/tcp
pi@raspberrypi:~ $
```
You can see now we have a service running inside our Swarm. Let's run the below command to see the running task of the service. 

```shellscript
pi@raspberrypi:~ $ docker service ps web
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE           ERROR               PORTS
jn3cdf50h5yy        web.1               nginx:latest        raspberrypi         Running             Running 8 minutes ago
pi@raspberrypi:~ $
```

Cool, we can see 1 task is running for the service `web` named `web.1`. If you want to increase the number of instance/ task for the service use bellow command:

```shellscript
pi@raspberrypi:~ $ docker service update --replicas 3 web
web
overall progress: 3 out of 3 tasks
1/3: running   [==================================================>]
2/3: running   [==================================================>]
3/3: running   [==================================================>]
verify: Service converged
pi@raspberrypi:~ $
```
As we asked our service to run 3 task instead 1 now we can run the command `docker ps` to list all the task. 

```shellscript
pi@raspberrypi:~ $ docker service ps web
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
jn3cdf50h5yy        web.1               nginx:latest        raspberrypi         Running             Running 16 minutes ago
yzlqjru481db        web.2               nginx:latest        raspberrypi         Running             Running 2 minutes ago
nb7a6w2lgvzb        web.3               nginx:latest        raspberrypi         Running             Running 2 minutes ago
pi@raspberrypi:~ $
```
WoW!! Now we can see 3 task are running for the service `web`. Now let's do some nasty thing to our service. Let's kill one conrainter using `docker stop` command and see what is happening to our service inside the `swarm`.
```javascript
pi@raspberrypi:~ $ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
6893a035fbb8        nginx:latest        "nginx -g 'daemon of…"   5 minutes ago       Up 5 minutes        80/tcp              web.2.yzlqjru481dblmddeijz1o70c
3980624ded92        nginx:latest        "nginx -g 'daemon of…"   5 minutes ago       Up 5 minutes        80/tcp              web.3.nb7a6w2lgvzby5o6fmmrwmok4
950638b3f7e8        nginx:latest        "nginx -g 'daemon of…"   20 minutes ago      Up 19 minutes       80/tcp              web.1.jn3cdf50h5yys8unst29dnmdj
pi@raspberrypi:~ $ docker stop 3980624ded92
3980624ded92
pi@raspberrypi:~ $ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
6d4b447aa0ac        nginx:latest        "nginx -g 'daemon of…"   4 seconds ago       Created                                        web.3.x8b2bp33rklh93fmjqsgvvi1l
6893a035fbb8        nginx:latest        "nginx -g 'daemon of…"   6 minutes ago       Up 6 minutes               80/tcp              web.2.yzlqjru481dblmddeijz1o70c
3980624ded92        nginx:latest        "nginx -g 'daemon of…"   6 minutes ago       Exited (0) 5 seconds ago                       web.3.nb7a6w2lgvzby5o6fmmrwmok4
950638b3f7e8        nginx:latest        "nginx -g 'daemon of…"   20 minutes ago      Up 20 minutes              80/tcp              web.1.jn3cdf50h5yys8unst29dnmdj
pi@raspberrypi:~ $
```
As you see I just stopped the container `3980624ded92` which was belongs to the task `web.1`. Let's list the service task again. 
```javascript
pi@raspberrypi:~ $ docker service ps web
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
jn3cdf50h5yy        web.1               nginx:latest        raspberrypi         Running             Running 23 minutes ago
yzlqjru481db        web.2               nginx:latest        raspberrypi         Running             Running 9 minutes ago
x8b2bp33rklh        web.3               nginx:latest        raspberrypi         Running             Running 3 minutes ago
nb7a6w2lgvzb         \_ web.3           nginx:latest        raspberrypi         Shutdown            Complete 3 minutes ago
pi@raspberrypi:~ $
```
Now we can see 4 entries for our service. But, the state of the last task is `Shutdown`. This proofs that `Swarm` keeps the history with it and helps us to make our application fault-tolerant. 

In our next chapter we will add 2 more nodes to our `Swarm mode` to play with it. 
