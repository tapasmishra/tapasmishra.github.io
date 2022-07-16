---
title: How to create a Kubernetes Cluster Using Minikube
header:
  image: /assets/images/kubernetes-banner.png
  teaser: /assets/images/kubernetes-banner.png
date: 2022-07-16 14:50:20 +0530
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

In my previous posts we learnt about Kubernetes concepts. Now time to create a small kubernetes cluster for our local experiments using minikube. This is not advisiable to use this tool in production environments. 

## Prerequisites:

To proceed with our cluster creation we need the following:

- Fimiliarity with kubernetes concept
- Fimiliarity with Docker installation steps
- At least 2 CPUs, 2GB of memory, and 20GB of disk space available to the environment where you are installing Minikube.

## Step 1 - Installing Docker

To run the minikube kubernetes cluster we need the runtime installed on the system. You can use `docker` or `kvm2` or `podman` or `vmware` or `virtualbox` or `qemu2` as your runtime environment. In this topic we will use Docker as the runtime environment. Let's install the Docker prior to install minikube:

There are lot of different methods available to install Docker on machine; out of which we will use convenience script method. Execute the below command to download the script and install Docker:

```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo bash get-docker.sh
```

### Post Installatation step for Linux

The Docker daemon binds to a Unix socket instead of a TCP port. By default that Unix socket is owned by the user root and other users can only access it using sudo. The Docker daemon always runs as the root user.

If you don’t want to preface the docker command with sudo, create a Unix group called docker and add users to it. When the Docker daemon starts, it creates a Unix socket accessible by members of the docker group.

To create the docker group and add your user:

- Create the docker group.
```
sudo groupadd docker
```
- Add your user to the docker group.
```
sudo usermod -aG docker $USER
```

Log out and log back in so that your group membership is re-evaluated

## Step 2 - Installing and Running Minikube

### Linux

To install the latest minikube stable release on x86-64 Linux using binary download execute the below command:

```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

### macOS

To install the latest minikube stable release on x86-64 macOS using binary download execute the below command:

```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
sudo install minikube-darwin-amd64 /usr/local/bin/minikube
```

### Windows

To install the latest minikube stable release on x86-64 Windows using .exe download:

- **Step 1 -** Download and run the installer for the [latest release.](https://storage.googleapis.com/minikube/releases/latest/minikube-installer.exe)
- **Step 2 -**   Add the minikube.exe binary to your PATH. Make sure to run PowerShell as Administrator.

```
$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)
if ($oldPath.Split(';') -inotcontains 'C:\minikube'){ `
  [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine) `
}
```
To begin using minikube, you can run it with the start command, which will automatically create a local Kubernetes cluster using multiple Docker containers and a recent stable version of Kubernetes.

```
minikube start
```

This will take a moment, and should produce output similar to the following.

```
ubuntu@kube-test:~$ minikube start
😄  minikube v1.26.0 on Ubuntu 20.04 (kvm/amd64)
✨  Automatically selected the docker driver. Other choices: ssh, none
📌  Using Docker driver with root privileges
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
💾  Downloading Kubernetes v1.24.1 preload ...
    > preloaded-images-k8s-v18-v1...: 405.83 MiB / 405.83 MiB  100.00% 8.59 MiB
    > gcr.io/k8s-minikube/kicbase: 386.00 MiB / 386.00 MiB  100.00% 3.76 MiB p/
    > gcr.io/k8s-minikube/kicbase: 0 B [_______________________] ?% ? p/s 1m11s
🔥  Creating docker container (CPUs=2, Memory=4000MB) ...
🐳  Preparing Kubernetes v1.24.1 on Docker 20.10.17 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass
💡  kubectl not found. If you need it, try: 'minikube kubectl -- get pods -A'
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
ubuntu@kube-test:~$
```

## Step 3 - Interact with your cluster

Inorder to interact with the kubernetes cluster you need to have `kubctl` command line installed on your system. To Install `kubectl` follow the below steps.

Download the latest release with the command:

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Install kubectl

```
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

Test to ensure the version you installed is up-to-date:

```
kubectl version --client
```
Now we have the kubectl command line tool installed on our system and we can start interacting with our cluster. 

```
kubectl get pods -A
```

The `-A` argument will return pods running in all namespaces.

```
ubuntu@kube-test:~$ kubectl get pods -A
NAMESPACE     NAME                               READY   STATUS    RESTARTS      AGE
kube-system   coredns-6d4b75cb6d-cxccd           1/1     Running   0             17m
kube-system   etcd-minikube                      1/1     Running   0             17m
kube-system   kube-apiserver-minikube            1/1     Running   0             17m
kube-system   kube-controller-manager-minikube   1/1     Running   0             17m
kube-system   kube-proxy-mqtxf                   1/1     Running   0             17m
kube-system   kube-scheduler-minikube            1/1     Running   0             17m
kube-system   storage-provisioner                1/1     Running   1 (17m ago)   17m
```

## Step 4 — Deploying and Testing a Sample App

Let's deploy `Google’s hello-app` in our cluster using `kubectl` command. Execute the below command to deploy:

```
kubectl create deployment web --image=gcr.io/google-samples/hello-app:1.0
```
Basically the above command will pull the `1.0` versoin of the `hello-app` image from `gcr.io` container regiesty. To access the deployed application now we have to expose it as a service, specifying a static port where it will be accessible with `--type=NodePort` and `--port=8080`:

```
kubectl expose deployment web --type=NodePort --port=8080
```

Now you can check whether the service is running with the kubectl get service command:

```
kubectl get service web
```

Remember, Kubernetes NodePorts use random ports, and your output will be different:

```
ubuntu@kube-test:~$ kubectl get service web
NAME   TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
web    NodePort   10.99.228.187   <none>        8080:32272/TCP   8s
ubuntu@kube-test:~$
```

Now we can use `minikube` command to retrieve a URL that is accessible outside of the container. 

```
minikube service web --url
```

Output of the above command will look like this:

```
ubuntu@kube-test:~$ minikube service web --url
http://192.168.49.2:32272
ubuntu@kube-test:~$
```

You can now try connecting to that URL. In order to do this, you’ll use a command line program called curl, which is popular for making different types of web requests. In general, if you want to verify whether a given connection should be working in a browser under ideal circumstances, you should always test first with curl.

```
curl http://192.168.49.2:32272
```

Output of the above curl command will look like this:

```
ubuntu@kube-test:~$ curl http://192.168.49.2:32272
Hello, world!
Version: 1.0.0
Hostname: web-6bf786c76b-d266x
ubuntu@kube-test:~$
```

Finally we learnt how to create a local kubernetes cluster to do some experiment before changing anything on production. In our next post we will learn how to deploy a production grade kubernetes cluster. 