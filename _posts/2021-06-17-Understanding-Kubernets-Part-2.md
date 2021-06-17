---
title: Understanding Kubernetes Part-II
header:
  image: /assets/images/kubernetes-banner.png
  teaser: /assets/images/kubernetes-banner.png
date: 2021-06-17 21:22:20 +0200
tags: 
  - kubernetes
  - k8s
  - networking
  - k8sSeries
categories: 
  - kubernetes
toc: true
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true

---

In our previous chapter from [#k8sSeries](https://www.linuxfunda.com/tags/#k8sseries) which is ["Understanding Kubernetes Part-I"](https://www.linuxfunda.com/kubernetes/Understanding-Kubernets-Part-1/) we discussed about what is Kubernetes and also we learnt various components of the Kubernetes cluster. In this chapter we will discuss or deep drive inside Pods Scalability and Kubernetes Networking Model. 

# Pods Scalability 

Lets assume that we have a Kubernetes Cluster which consists of one `Control Pannel` node and two `Worker nodes`. We instructed kubernetes using `kubectl` to deploy `pod` with replica set 3. As a result it deployed 2 # of pods on Worker Node A and 1 # of pod in Worker Node B to distribute the workload.  

<figure>
  <a href="/assets/images/kuber-cluster-k8s-II-serise.PNG"><img src="/assets/images/kuber-cluster-k8s-II-serise.PNG"></a>
  <figcaption>"Kubernetes Cluster"</figcaption>
</figure>

Now just think about what will happen if the Worker Node B will go offline due to certain resons. Answer to this quesion is the controller will deploy one more replica of the pod on Woker Node A.

<figure>
  <a href="/assets/images/kuber-cluster-k8s-II-serise-exmpII.PNG"><img src="/assets/images/kuber-cluster-k8s-II-serise-exmpII.PNG"></a>
  <figcaption>"Kubernetes Cluster"</figcaption>
</figure>

Why the controller deployed the 3rd replica set to the Worker Node A and not Control Panel node? And answer to this is by default Control panel node is desiged to only host the sytem pods. You can twik kubernetes cluster configuration to host deployment pods on the Control Panel node but it's not advisable to do in production clusters. You can experiment in your development cluster.  

# Kubernetes Networking Model

Since Kubernetes cluster consist of various nodes and pods, understandig how they communicate with each other is very essesintial. Kubernetes provides IP address to each pod hence it doesn't require to map host ports to the container ports. All Pods can communicate with all other Pods without using network address translation (NAT). All Nodes can communicate with all Pods without NAT.

Kubernetes networking situation:
- Container-to-Container 
- Pod-to-Pod
- Pod-to-Service
- Internet-to-Service

In Linux each process on your machine communicates inside a network namespace. This namespace creates a logical networking stack with its own network devices, firewall rules, and routes. When you run a process, it is assigned by default to your root network namespace. This provides the process with external access. Let's discuss about the above Kubernetes network variations one by one.

## Container-to-Container:

In Kubernetes containers are grouped as pods, each with a shared namespace. In pod each container will have same IP and port and port space. They can use localhost to communicate with each other as they are within same namespace. 

## Pod-to-Pod

As we know to distribute load Kubernetes Controller can deploy pods across several Worker Nodes or it could be on the same Worker Node as well. In Kubernetes each pod has a real IP address and each pod communicates with other pods using that IP address. Now we have to understand how the communication is happening in between pods using Real IP address, where they may or may not located in a same worker node. 

Let's start discussing pods on the same node. From pods prospecitve each pods exists in it's own Ethernet namespace that needs to communicate with other network namespace on the same node. In Linux namespaces can connect with each other using Virtual Ethernet Device or veth pair consisting of two vertual interface that can spread over multiple namespaces. To connect pod network namespace we can assign one side of the veth pair to the root namespace and other to the pod's network namespace. Now the veth pair will work like a patch cable and allow trafic to flow between. Same configuration needs to be applied to the all pods exists on the Worker node. After the pod got connected to the root namespace it will be able to connect to the other pods using a network bridge.

A Linux Ethernet bridge is a virtual Layer 2 networking device used to unite two or more network segments, working transparently to connect two networks together. The bridge operates by maintaining a forwarding table between sources and destinations by examining the destination of the data packets that travel through it and deciding whether or not to pass the packets to other network segments connected to the bridge. The bridging code decides whether to bridge data or to drop it by looking at the MAC-address unique to each Ethernet device in the network.

So far we were able to know how the packtes moves in between pods which are located in a same machine. But now we will discuss how a pod will communicate with another pod located on a different machine. Each Node on the Kubernetes cluster is assigned with a CIDR block specifying the IP addresses available to the Pods on that Node. Once the traffic destinated for a CIDR reaches at the Node, it's the Node's responsibility to forward that traffic to the relevant pod. But if the CIDR is not there in the Node then the packate will be routed to the root namespace i.e. the `eth0` device. Now the packet will enter in the network and will be routed to the Node which has the correct CIDR and the bridge interface of that Node will route the packate to it's correct pod.