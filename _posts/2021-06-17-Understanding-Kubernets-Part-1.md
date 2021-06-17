---
title: Understanding Kubernetes Part-I
header:
date: 2021-06-17 16:22:20 +0200
tags: 
  - kubernetes
  - k8s
categories: 
  - kubernetes
toc: true
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true

---

## Description

In short Kubernets which is also knows as "`k8s`" is a container orchestration platrom; facilating automation around deployment, management and scaling containrized applications (`Cloud-Native` or `Micro Services`). It can be build and run on distributed systems over a cluster of machines. The cluster machines can span across Public, private and Hybrid Cloud. 

## Kubernets Functionalities

Kubernetes has multiple funcionalities which makes it as a greater and mostly adoptable container orchestration platfrom. Here we will discuss about few popular funcionalities, those are playing critial role in Kubernets. 

| Sl. No. | Name |
| :------ | :--- |
| 1 | Kubernetes API Server |
| 2 | Controllers |
| 3 | Pods |
| 4 | Nodes |
| 5 | Service |
| 6 | Kubelet |
| 7 | kubectl | 

### Kubernetes API Server

In Kubernets everything is API calls which is basically served by the Kubernets API Server (`kube-apiserver`). The API Server services REST operations and provides the frontend to the cluster's shared state through which all other components interact. Basically the API server is a gateway to an etcd datastore that maintains the desired state of your application cluster.

### Controllers

In Kubernetes, a controller is a control loop that watches the shared state of the cluster through the apiserver and makes changes attempting to move the current state towards the desired state.

### Pods

A group of one or more containers deployed to a single node. Also, this the smallest deployable object for building applications. All containers in a pod share an IP address, IPC, hostname, and other resources. Pods abstract network and storage from the underlying container. This lets you move containers around the cluster more easily.

### Nodes

These machines perform the requested tasks assigned by the control plane. These can be bare metal, virtual machines, or anything else.

### Service

This decouples work definitions from the pods. Kubernetes service proxies automatically get service requests to the right pod—no matter where it moves in the cluster or even if it’s been replaced.

### Kubelet

This service runs on nodes. It registers the node with the API server using hostname. Also, it reads the container manifests, and ensures the defined containers are started and running.

### kubectl

kubectl is the command line tool for Kubernetes which controls the Kubernetes cluster manager.

