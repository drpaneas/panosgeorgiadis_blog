+++
categories = ["tutorial"]
date = "2017-05-08T22:08:35+02:00"
tags = ["tutorial", "kubernetes", "docker", "containers", "cluster"]
title = "Kubernetes with Docker"

+++

## Course Syllabus

### Introduction

In the introduction we are going to talk about the syllabus. We are also going
to talk about Kubernetes in order to get you familiar with what we are going to
see later. Then we are going to see what Docker is why this is the preferred
technology that is preffered for Kubernes to manage. Talk a little bit about
containers versus virtual machines and their key differences. Then talk a little
bit about containers in order to have an understanding of how they operating not
inside Kubernetes but within a host. We will talk a little bit about DockerHub.
That we will dive back to Kubernetes and talk about its architecture since we now
know more about Docker. Also talk about YAML which is used to build configuration
files for Kubernetes.

* Course Syllabus
* Introduction to Kubernetes
* What is Docker?
* Container vs VMs
* Docker Architecture
* The DockerHub
* Kubernetes Architecture
* Introduction to YAML

### Setup and Configuration

Then we will be having a section of Setup and Configuration about packages and the
dependencies we need to install in different pieces of our system, mainly the master
and the minions. We will install and configure the Kube Master and the Kube Minions
(nodes). And then, when we have that done we will use the native k8s main facility
environment for querying and running and verifying that our configuration meets our
expectations.

* Packages and Dependencies
* Install and Configure Kube Master
* Install and Configure Kube Minions (Nodes)
* Kubctl: Querying and Running Environment

### Docker Fundamentals

As soon as this is done, we have to spend some time to see some Docker fundamentals.
This is not going to be a deep learning course, but that depends on the time and on
your curiosity. We will pull an image, we will build a container from that image and
then customize it to our needs. That will give you a fundamental level of
understanding the rest of the Kubernetes.

* Pulling an Image
* Running a Container from an Image
* The Dockerfile
* Building a Custom Image from a Dockerfile

### Pods, Tags, Labels and Services

Then we will move back to Kubernetes talk a little bit more about pods, tags, labels
and and services. We will create and deploy a Pod Definition -- which is the reason we
did the introduction to YAML -- we will create and deploy a service, and talk about
how to use our containers to use these services. We will talk about how to view Pods
and their characteristics using the K8s toolset.Then we will look how the docker
containers look when they have been deployed by K8s on the individual minions, so you
understand what pieces you have to run and how it looks like.

* Create and Deploy a Pod Definition
* Create and Deploy a Service
* How to Use Services
* Viewing Pod and Service Characteristics
* Docker Containers Running on Minions
* Tags and Labels

### Clusters

Then we will move on into clusters and we will talk about how we define a state.
Ultimately K8s drives our cluster environment to a stated configuration and maintains
the number of Pods and containers based on the configuration we have set. So, we will
create a cluster environment and then we will see how we can manage that. We will talk
about replication and the controller which is involved in replicating our Pod and
Containers in different minions. We will talk how we can scale our application both in
terms of an existing cluster and new clusters so that is scales up approrpriately. We
will also look how the recovery process takes place in the k8s environment and see how
k8s moves the workload to another Pod (self-healing). And then we will look at our error
reporting and logging options.

* Driving to a Defined State
* Creating a Clustered Container Environment
* Managing our cluster
* Replication
* Scaling
* Self-Healing
* Error reporting and logging

Last, we will dig into some use cases, such as setup an Apache WebSite Cluster and and
Guestbook (Multiple Services) implemention from the K8s website.

## What is Kubernetes

By definition, Kubernetes is an open source container cluster manager, and it's
usually referred to by its internal name withing Google development - **k8s**.
Google donated it to the open source world as a *"seed technology"* at 2015, to
the newly formed *CNCF - Cloud Native Computing Foundation*, which established
partnership with *The Linux Foundation*. The primary goal of Kubernetes is to
provide a platform for automating deployment, scaling and operations of
application containers across a cluster of hosts. In fact, withing Google they
have been using Kubernetes-like tools in order to run daily billions of
containers within their services.

### Design Overview

Kubernetes is built through a set of components (building blocks or
*primitives*), which when they are used collectively, then they provide a
method for the deployment, maintenance and scalability or container based
application clusters. These *primitives* are designed to operate without
requiring any kind of special knowledge from the user. They are really easy to
work with and they are also highly extensible through an API. All the
components of Kubernetes (internal, extensions and containers) are making use
of the API.

Although Kubernetes was originally designed withing Google's infrastructure,
nowadays it's considered the default option for other majer public cloud
providers, such as AWS and Azure.

### Components

So, what are the components (building blocks) that are going into to
Kubernetes:

* Nodes (often referred to as minions)
* Pods
* Labels
* Selectors
* Controllers (multiple kinds of them)
* Services
* Control Pane (the master controller)
* API

Some of these topics are relatively involved, so you need to practice by
yourself and manage your own Kubernetes environment, in order to be able to
understand at 'experience' level, how these things work.

### Architecture

In a very-very high-level view, we have these kubernetes building-blocks we
talked about. This is really how the whole environment looks like:

![Kubernetes Architecture](/images/kubernetes_architecture.png)

We have *one* **master/controller**  and we have *1-to-N* **nodes** which can
have *1-to-N* **pods** and each pod can have *1-to-N* **containers**. How many
of those *N* things you need, it is based upon the desired state of the
configuration which is located in the *master/controller* via *YAML* form.
Also, this depends on the minion resources (either physical or virtual) that
you can allocate.

Each node has to have at least some container management engine installed, such
as Docker.

#### Nodes (Minions)

You can think of these as *container clients* and they can be either physical
or virtual. Also, your container management engine has to be installed on, (such
as Docker) and hosts the various containers within your managed cluster.

Furthermore, each minion will run **ETCD**, as well as the *master/controller*.
ETCD is a key-pair management and communication service, used by Kubernetes
for exchanging messages and reporting on cluster status. It's a way for us to
keep everything in-sync and exchange information from the individual minions to
our master controller and as well as our Kubernetes Proxy -- that's the other
item that runs on each of the minions. So, for each minion there are two things
that are running and are specific to Kubernetes, which is ETCD and Kubernetes
Proxy, and last but not least, Docker has to be installed as well. During this
tutorial we will go over all these packages and install them.

#### Pods

The simplest definition is that a pod consists of one or more containers. Going
further, these containers are then guaranteed (by the master controller) to be
located on the same host machine in order to facilitate shared resources
(volumes, services mapped through ports). Pods are assigned with unique IPs
within each cluster, so these allow our application to use ports for the Pod
without having to worry about conflicting port utilization. In another words, I
can have multiple Pods running at port `80` or `443` or `whatever`, on the same
host, because I am not re-mapping those ports but I am giving each Pod a unique
IP address within the cluster, so I don't have to worry about port conflicts.

Pods can contain definitions of disk volumes or share, and then provide
access from those to all the containers within the pod.

An the finally, pod management is done through the API or delegated to a
controller.

#### Labels

Clients can attach "key-value pairs" to any object in the system (like Pods or
Nodes). These become the labels that identify them in the configuration and
management of them. And this is where *Selectors* come in, because they are
used in conjuction.

#### Selectors

Label Selectors represent queries that are made against those labels. They
resolve to the corresponding matching objects and will show when we are
managing our Pods in our cluster how we use the built-in API and tools for
Kubernetes in order to get a selection of objects based on these label
selectors.

These two items (Labels and Selectors) are the primary way that grouping is
done in Kubernetes and determine which components that a given operation
applies to when indicated.

#### Controllers

These are used in the management of your cluster. Controllers are the mechanism
by which your desired configuration state is enforced. In fact the controllers
main purpose is to enforce the desired state of your configuration to your
cluster. They manage a set of pods and, depending on the desired configuration
state, may engage other controllers to handle replication and scaling (through the
**Replication Controller**) of XX number of containers and pods across the cluster.
It is also responsible for replacing any container in a pod that fails or any
container in the cluster that fails (based on the desired state of the cluster).
And again, all these are representing a desired state written in YAML files.

Other controllers that can be engaged include **DaemonSet Controller** which
enforces a 1:1 ration of pods to minions and a **Job Controller** that runs
pods to "completion", such as in batch jobs. Each set of pods any controller
manages, is determined by the label selectors that are part of its definition.

## Setup and Configuration

### Packages and Dependencies

One of the first things we need to do is to install NTP. NTP has to be 
enabled and running in **all** of the servers we are going to have in
our cluster. In this example, I have four terminals open:

```
- centos-master		[172.31.28.38] 		[54.154.199.96]
- centos-minion1	[172.31.120.121]	[54.171.6.143]
- centos-minion2	[54.246.160.157]	[172.31.110.96]
- centos-minion3	[54.246.220.156]	[172.31.23.169]
```

So we are going to use 3 minions as worker nodes, and then 1 server as the
master in our cluster. So, lets us install ntp:

```bash
yum install -y ntp
```

We want to be sure that all the servers in our cluster are time-synchronised
down to the second. This is because we are going to use a service that logs
what happen to our cluster upon special conditions and it is important that
our servers are reporting as close and as accurate as possible.

```bash
systemctl enable ntpd
systemctl start ntpd
```

Feel free to check the status in order to verify that is running:

```bash
systemctl status ntpd
```

As soon as we finish with the installation of `ntp` in all of our servers in
the cluster, then we need to make sure that we have full name resolution for
the servers in our environment. If you are installing this locally and you
do not port-forward this ports externally, then it is best to use your internal
IP addresses rather than the external ones. Similarly, this means that you cannot
use the name of the server, because this will refer to the external IP address.
But, to make things look as they supposed to be, I am going to create a file
`/etc/hosts` with the corrersponding nickname of these servers. This file has to
be into each server that is part of my cluster and it has to resolve the nicknames
against the internal/private IP address. In that case, I will be able to use those
internal hostnames in my configuration file:

```bash
vim /etc/hosts

172.31.28.38    centos-master
172.31.120.121  centos-minion1
172.31.110.96   centos-minion2
172.31.23.169   centos-minion3
```

Put this into every server and then make sure that all of them can ping each other
based on those nicknames:

```bash
ping centos-master
ping centos-minion1
ping centos-minion2
ping centos-minion3
```

As soon as you finished this and your verified that all server can ping each other
based on their internal IP address, it is about time to add a repository that we
can use to install the latest version of `Docker` and `Kubernetes`.

```bash
vim /etc/yum.repos.d/virt7-docker-common-release.repo
[virt7-docker-common-release]
name=virt7-docker-common-release
baseurl=http://cbs.centos.org/repos/virt7-docker-common-release/x86_64/os/
gpgcheck=0
```

Save it and then update the cache of the system:

```bash
yum update
```

Make sure you do this for all the server in the cluster.

Next, make sure that all firewalls are disabled, since this is a demo and not
a production environment, so we need to avoid problems of port filtering.

```bash
systemctl status iptables  --> disabled
systemctl status firewalld --> disabled
```

Ok, now it is about time to install `etcd`. This is the mechanism that helps
all the members of the cluster to communicate and advertise their status,
availability, and their logs. So, we need to install `etcd` and `kubernetes`.
These two, will also pull `cadvisor` which for containers and containerized
apps. Then we will, start configuring the master and the minion, but right
now I will finish by installing the software:

```
yum install -y --enablerepo=virt7-docker-common-release kubernetes docker
```

### Configure the Master

Since we are into the master, we first need to configure the Kubernetes
itself:

```bash
vim /etc/kubernetes/config

# If you want to log errors in the systemd-journal for Kubernetes
KUBE_LOGTOSTDERR="--logtostderr=true"

# If you want to have debug level log files in your systemd journal
# 0 is the most verbose (debug level)
KUBE_LOG_LEVEL="--v=0"

# Disable privileged docker containers within the cluster
KUBE_ALLOW_PRIV="--allow-privileged=false"

# Define the API Server
# How the controller-manager, scheduler, and proxy find the apiserver
KUBE_MASTER="--master=http://centos-master:8080"

# Define the ETCD Server
KUBE_ETCD_SERVER="--etcd-servers=http://centos-master:2379"
```

This is the initial configuration for our master kubernetes node. Notice
that I used domain names, and not IP address, in that way if the IP address
of the Kubernetes ETCD server changes, the cluster will still be able to
resolve it, using the DNS server.

#### Configure ETCD

Install it:

```bash
yum install -y etcd
```

Configure it:

```bash
vim /etc/etcd/etcd.conf

# It is OK to leave these two with their default values
ETCD_NAME=default
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"

# Listen on all interfaces and accept connections from anywhere
ETCD_LISTEN_CLIENT_URLS="http://0.0.0.0:2379"

# Listen on all interfaces and accept connections from anywhere
ETCD_ADVERTISE_CLIENT_URLS="http://0.0.0.0:2379"
```

The master kubernetes nodes is the only place where we are going
to be running ETCD.

#### Configure API

```bash
vim /etc/kubernetes/apiserver

# Accept connections from all interfaces
KUBE_API_ADDRESS="--address=0.0.0.0"

# Make sure that port for API is listening on 8080
KUBE_API_PORT="--port=8080"

# Make sure that port for Kubelet is listing on 10250
KUBELET_PORT="--kubelet-port=10250"

# Comma separated list of nodes in the etcd cluster
KUBE_ETCD_SERVERS="--etcd-servers=http://127.0.0.1:2379"

# Address range to use for services (Feel free to change it based on your environment)
KUBE_SERVICE_ADDRESSES="--service-cluster-ip-range=10.254.0.0/16"

# Add your own!
KUBE_API_ARGS=""
```

#### Start the services

First, you need to start with `etcd`:

```bash
systemctl enable etcd
systemctl start etcd
systemctl status etcd
```

Then, follow up with `kube-apiserver`:

```bash
systemctl enable kube-apiserver
systemctl start kube-apiserver
systemctl status kube-apiserver
```

Follow up with `kube controller manager`:

```bash
systemctl enable kube-controller-manager
systemctl start kube-controller-manager
systemctl status kube-controller-manager
```

Last, `kube-scheduler`:
```bash
systemctl enable kube-scheduler
systemctl start kube-scheduler
systemctl status kube-scheduler
```

Make sure that all of these 4 services are started. They **have** to be up and running
otherwise, it makes no sense to ignore them and configure the minions. This is the univeral
configuration for our cluster.

### Configure Minions

The following configuration has to be applied to all the minions
of the cluster. The first thing we want to do is to apply our
Kubernetes configuration:

```bash
vim /etc/kubernetes/config
# logging to stderr means we get it in the systemd journal
KUBE_LOGTOSTDERR="--logtostderr=true"

# journal message level, 0 is debug
KUBE_LOG_LEVEL="--v=0"

# Disable privileged docker containers from running into the cluster
KUBE_ALLOW_PRIV="--allow-privileged=false"

# How the minion talks with the API Server
KUBE_MASTER="--master=http://centos-master:8080"

# How the minion talks with the ETCD Server
KUBE_ETCD_SERVERe="--etcd-servers=http://centos-master:2379"
```

Next, we are going to edit the `kubelet` configuration:

```bash
vim /etc/kubernetes/kubelet

# kubernetes kubelet (minion) config

# The address for the info server to serve on (set to 0.0.0.0 or "" for all interfaces)
KUBELET_ADDRESS="--address=0.0.0.0"

# The port for the info server to serve on (it has to corresponds the port of the master)
KUBELET_PORT="--port=10250"

# You may leave this blank to use the actual hostname
KUBELET_HOSTNAME="--hostname-override=centos-minion1"

# location of the api-server
KUBELET_API_SERVER="--api-servers=http://centos-master:8080"

# Add your own!
KUBELET_ARGS=""
```


Now start and enable the services:

```bash
systemctl enable kube-proxy
systemctl start kube-proxy
systemctl status kube-proxy
```

```bash
systemctl enable kubelet
systemctl start kubelet
systemctl status kubelet
```

```bash
systemctl enable docker
systemctl start docker
systemctl status docker
```

As soon as we verify that all of the are working as expedted, now we have
to verify also that docker works. To do, I am going to use a simple
containerized app, called `hello world` -- what a surprise.

```bash
docker pull hello-world
docker images
docker run hello-world
docker ps
docker ps -a
```

Now we have our minion configuration complete and actually it has to
be registered against our master. Please make sure that the same config
exists in the rest 2 minions, before you proceed.


