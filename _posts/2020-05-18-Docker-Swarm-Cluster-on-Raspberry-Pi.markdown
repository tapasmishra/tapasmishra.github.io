---
layout: post
title: Setup Docker Swarm Cluster on Raspberry Pi
image: docker-swarm.jpg
date: 2020-05-18 13:35:20 +0200
tags: [Docker, Linux]
categories: docker
---

### Hardware 

Let us setup a 3 node Sawrm Cluster

| Host Name      | IP Address |
|:---------------|:-------------------:|
| swarm-node1         | 192.168.1.130   |
| swarm-node2         | 192.168.1.131   |
| swarm-node3         | 192.168.1.132   |

Login to `swarm-node1`, `swarm-node2` and `swarm-node3` to check the `Docker` version. Make sure to match the `Docker` version across all these instances.   