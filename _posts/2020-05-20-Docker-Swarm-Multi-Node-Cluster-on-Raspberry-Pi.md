---
title: Setup Docker Swarm Multi node Cluster on Raspberry Pi
header:
  image: /assets/images/swarm.png
  teaser: /assets/images/swarm.png
date: 2020-05-21 13:35:20 +0200
tags: 
  - Docker 
  - Linux 
  - Swarm
  - Cluster
categories: 
  - docker
toc: true
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true

---

## Description

In the [previous chapter](https://www.linuxfunda.com/docker/Docker-Swarm-Cluster-on-Raspberry-Pi/) we learnt how to create a Swarm Mode on a single node. We have created a service named `web` on our cluster and also we have scaled our service. But now in this article we will add more 2 nodes to our cluster. We will scale our service to different nodes and we will see how fault tolerant our serive is? We will take nodes offline to prove that our service is fault tolerant. Letus start adding the nodes to the cluster.

## Hardware

Please go though the beolow table for more clarity on the hostname's of the RaspberryPi's and their IP's

| Sl. No.    | Host Name     | IP Address     |
| :------------- | :----------: | -----------: |
|  1 | swarm-node-1   | 192.168.0.131    |
| 2  | swarm-node-2 | 192.168.0.132 |
| 3 | swarm-node-3 | 192.168.0.133 |

## Install Docker 
If docker is not installed on your RaspberryPi then follow the instruction's from [How to install docker on raspberry Pi](https://www.linuxfunda.com/docker/raspberrypi/how-to-install-docker-on-raspberry-pi/)

You have to install Docker on each of the node. 

## Create Docker Swarm

If docker swarm is not created yet on any of your raspberry pi's then you can follow the instruction from our previous chapter to create it:  [Docker Swarm Cluster on Raspberry Pi](https://www.linuxfunda.com/devsite/docker/Docker-Swarm-Cluster-on-Raspberry-Pi/)

You need to follow the above insrucion for any one node only. We will learn the next steps how to add additional nodes to any existing cluster in this chapter. 

### Add node's to our cluster
In our previous chapter we have already installed `Docker Engine` on one of our node. So, we will directly move foward from adding the node to cluster. 
If you remember while initializing the Swarm it gave us a command to add aditional nodes. It's OK if you have that or we can get that information again using the below command:

```ruby
pi@swarm-node-1:~ $ docker swarm join-token worker
To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-0490adm0y5alpu48ydophj3b4lms61bkgofa0l9rcvuar0iyjf-4wturco6v3tddcq1fvtgt8v8a 192.168.0.131:2377
    
pi@swarm-node-1:~ $
```
Now we can run the above command on both of our nodes to add them to the cluster. Let's do that one by one.
On Node-2
```ruby 
pi@swarm-node-2:~ $ docker swarm join --token SWMTKN-1-0490adm0y5alpu48ydophj3b4lms61bkgofa0l9rcvuar0iyjf-4wturco6v3tddcq1fvtgt8v8a 192.168.0.131:2377
This node joined a swarm as a worker.
pi@swarm-node-2:~ $
```
On Node-3
```ruby
pi@swarm-node-3:~ $ docker swarm join --token SWMTKN-1-0490adm0y5alpu48ydophj3b4lms61bkgofa0l9rcvuar0iyjf-4wturco6v3tddcq1fvtgt8v8a 192.168.0.131:2377
This node joined a swarm as a worker.
pi@swarm-node-3:~ $
```
Now both of the nodes has been joined as a worker node in our cluster. We can now see our node stastus on our Master/ leader node. 

```ruby
pi@swarm-node-1:~ $ docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
jdu8ge8rafvdsiu9x9nhvhymp *   swarm-node-1        Ready               Active              Leader              19.03.8
up883y2rfo528yybtgx5jao4q     swarm-node-2        Ready               Active                                  19.03.9
ytfasal6q5eatwzq4usvhq5mu     swarm-node-3        Ready               Active                                  19.03.9
pi@swarm-node-1:~ $
```

## Scale the service on Cluster
Now we have a multinode Swarm cluster. If you remember in the previous chapter we had created a service named `web` with three container. Let's first see the status of the service and then we will scale it to five. 

Status of the service
```ruby
pi@swarm-node-1:~ $ docker service ps web
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE           ERROR               PORTS
qmxppxnbwypk        web.1               nginx:latest        swarm-node-1        Running             Running 8 minutes ago                      
j4bvyaoo3yds        web.2               nginx:latest        swarm-node-1        Running             Running 7 minutes ago                      
6egofavxae01        web.3               nginx:latest        swarm-node-1        Running             Running 7 minutes ago                      
pi@swarm-node-1:~ $
```
See, stil all of the containers / tasks of the service are running on node-1. It is because; by the time we created the service we had only one node. We have increased the cluser capacity now by adding two more nodes. Now, let us see where the new containers will get created if we scale the service to 5.

Run the below commad to scale up the service 
```ruby
pi@swarm-node-1:~ $ docker service update --replicas 5 web
web
overall progress: 5 out of 5 tasks
1/5: running   [==================================================>]
2/5: running   [==================================================>]
3/5: running   [==================================================>]
4/5: running   [==================================================>]
5/5: running   [==================================================>]
verify: Service converged
pi@swarm-node-1:~ $
```
Now let us again see the status of the task's / containers of the service. 
```ruby
pi@swarm-node-1:~ $ docker service ps web
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE                ERROR               PORTS
qmxppxnbwypk        web.1               nginx:latest        swarm-node-1        Running             Running 18 minutes ago                     
j4bvyaoo3yds        web.2               nginx:latest        swarm-node-1        Running             Running 17 minutes ago                     
6egofavxae01        web.3               nginx:latest        swarm-node-1        Running             Running 17 minutes ago                     
7k8a2f63037f        web.4               nginx:latest        swarm-node-3        Running             Running about a minute ago                 
leieatzkb88s        web.5               nginx:latest        swarm-node-2        Running             Running about a minute ago                 
pi@swarm-node-1:~ $
```
As expected new two tasks / containers are got created on Node-2 and 3. 

## Promoting nodes in the cluster

It's always better to have 3 masters in a cluster. To promte the nodes as manager in an exsting cluster you need to run the below command

```ruby
pi@swarm-node-1:~ $ docker node promote swarm-node-2
Node swarm-node-2 promoted to a manager in the swarm.
pi@swarm-node-1:~ $ 
pi@swarm-node-1:~ $ docker node promote swarm-node-3
Node swarm-node-3 promoted to a manager in the swarm.
pi@swarm-node-1:~ $ 
pi@swarm-node-1:~ $ docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
jdu8ge8rafvdsiu9x9nhvhymp *   swarm-node-1        Ready               Active              Leader              19.03.8
1wmbzasnepdq6d5hebn4nz0ji     swarm-node-2        Ready               Active              Reachable           19.03.9
ytfasal6q5eatwzq4usvhq5mu     swarm-node-3        Ready               Active              Reachable           19.03.9

```

In the next chapter we will discuss more about service deployment on our cluster. 