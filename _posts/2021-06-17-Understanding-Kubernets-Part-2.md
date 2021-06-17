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

Let's discuss about the above network variations one by one.

## Container-to-Container:

In Linux each process on your machine communicates inside a network namespace. This namespace creates a logical networking stack with its own network devices, firewall rules, and routes. When you run a process, it is assigned by default to your root network namespace. This provides the process with external access.

In Kubernetes containers are grouped as pods, each with a shared namespace. In pod each container will have same IP and port and port space. They can use localhost to communicate with each other as they are within same namespace. 

## Pod-to-Pod
