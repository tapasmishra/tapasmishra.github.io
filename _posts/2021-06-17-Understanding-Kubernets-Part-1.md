---
title: Understanding Kubernetes Part-I
header:
  image: /assets/images/kubernetes-banner.png
  teaser: /assets/images/kubernetes-banner.png
date: 2021-06-17 16:22:20 +0200
tags: 
  - kubernetes
  - k8s
  - k8sSeries
categories: 
  - kubernetes
toc: true
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true

---

In short Kubernets which is also knows as "`k8s`" is a container orchestration platform; facilitating automation around deployment, management and scaling containerized applications (`Cloud-Native` or `Micro Services`). It can be build and run on distributed systems over a cluster of machines. The cluster machines can span across Public, private and Hybrid Cloud. 

# Kubernets Components

Kubernetes has multiple functionalities which makes it as a greater and mostly adoptable container orchestration platform. Here we will discuss about few popular functionalities, those are playing critical role in Kubernets. 

| Sl. No. | Name |
| :------ | :--- |
| 1 | Control Plane node|
| 2 | Nodes |
| 3 | Kubernetes API Server |
| 4 | etcd |
| 5 | Scheduler |
| 6 | Controllers |
| 7 | Kubelet |
| 8 |  Kube-proxy |
| 9 | Container Runtime |
| 10 | Pods |
| 11 | Service |
| 12 | kubectl | 
| 13 | Useful Ad-on Pods|

<figure>
  <a href="/assets/images/kubernetes-architecture.png"><img src="/assets/images/kubernetes-architecture.png"></a>
  <figcaption>"Image Source: google"</figcaption>
</figure>

## Control Plane node

A control plane node is also known as Master node. It implements the major control options of the cluster. It coordinates cluster operations, monitoring,  pod scheduling and finally it is he main access point for he cluster operation. 

### Kubernetes API Server

In Kubernets everything is API calls which is basically served by the Kubernets API Server (`kube-apiserver`). The API Server services REST operations and provides the frontend to the cluster's shared state through which all other components interact. Basically the API server is a gateway to an etcd datastore that maintains the desired state of your application cluster.

### etcd

Persists the state of the api objects as Key-value pairs. 

### Scheduler

Watches API server for any unscheduled pods and scheduled it on nodes. Evaluates the resource requirements for the pods in terms of CPU, memory and storage and make sure their availability before placing these pods inside a specific node of the cluster. It's responsible to span the pods across all the nodes. 

### Controllers

In Kubernetes, a controller is a control loop that watches the shared state of the cluster through the apiserver and makes changes attempting to move the current state towards the desired state.

## Nodes

Nodes are also known as worker nodes. These machines perform the requested tasks assigned by the control plane. Actually this is the place where application pods will run.  Nodes are responsible to starting up the pods and the containers that required for the pods. Nodes implement networking to ensure the reachability to the services and pods running on the worker node. Finally nodes can be bare metal, virtual machines, or anything else.

### Kubelet

This service runs on nodes. It registers the node with the API server using hostname. Also, it reads the container manifests, and ensures the defined containers are started and running.

### Kube-proxy 

Kube-proxy runs on nodes and responsible for the pod networking. It manages the iptables and also responsible for the load balancing. 

### Container Runtime

This also runs inside the worker nodes and this is the actual run time environment for the containers which supports to run the pod. It is responsible to pull the container from the container registry and provide the execution environment for that container. 

### Pods

A group of one or more containers deployed to a single node. Also, this the smallest deployable object for building applications. All containers in a pod share an IP address, IPC, hostname, and other resources. Pods abstract network and storage from the underlying container. This lets you move containers around the cluster more easily.

### Service

This decouples work definitions from the pods. Kubernetes service proxies automatically get service requests to the right pod—no matter where it moves in the cluster or even if it’s been replaced.

## kubectl

kubectl is the command line tool for Kubernetes which controls the Kubernetes cluster manager.

## Useful Ad-on Pods

Ad-on pods or the special pods on the cluster provides special services to the cluster. 

### DNS

DNS service will provide the DNS to the cluster using core DNS. All the pods, nodes are services will register their names in the DNS server. It's basically used for the service discovery of the application deployed inside the cluster. 

### Ingress

Ingress pods are basically http or layer 7 load balancer. 

### Dashboard

This service will provide you a Web Based administrative UI to administrate the Kubernetes cluster. 