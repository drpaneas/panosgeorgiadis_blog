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

### Interact with the cluster

The main utility for interacting with the kubernetes cluster, is called
`kubectl` -- kubecontrol.

```bash
man kubectl
```

Usually, either you **get** sth or **set** something. For example,
if you want to see the nodes which are registerested againsto our
master:

```bash
kubectl get nodes
```

But, how do I know what are the potential parameteres for that command?

```bash
man kubectl-get
```

For example, I can get some information about those nodes:

```bash
kubectl describe nodes
```

I can also request this in JSON format:

```bash
kubectl get nodes -o jsonpath='pattern'
```

In the future, we will do `kubectl get pods`. A minion is a node
that is registered in our cluster in which we can install pods on.
Pods contain containers of things, such as services (apache, ngix, etc).

## Run Containers in Pods

So, after having our environment set and all of our nodes are registered
against the master node, it is about time to run containers inside of
Pod in our cluster. For now, we are going to go through the initial
configuration of setting up Pods in our 3 minions. For the shake of
less complexity, I am going to turn-off the 2 out of the 3 minions,
so I am going to to deploy Pods into a single node.

```bash
[root@drpaneas1 ~]# kubectl get nodes
NAME             STATUS     AGE
centos-minion1   Ready      19h
centos-minion2   NotReady   19h
centos-minion3   NotReady   19h
```

As I said, 3 minions are registered, but only one of them is ready
to use.

### Create a Pod

First of all, we need to create a Build directory:

```bash
mkdir Builds
cd Builds
```

Now there are two ways that we can begin to generate Pods which will
contain Docker containers in our environment.

1. We can create configuration file using JSON
2. We can create configuration file using YAML

From the configuration point of view, I am going to focus only to YAML.
From a definition stand point, YAML is better for configuration and
JSON is preffered as output. But, for input and configuration, I like
to use YAML because I think it is simpler to use.

So, to create a Pod, we need to create a *definition*. So, *keep in mind*
that a Pod Definition is like telling Kubernetes the *desired state of 
our Pod*. It is really important to understand that the desired state
is a key concept, because it is the key reponsibility of Kubernetes to
ensure that the current state of the cluster matches the defined as
desired state. So, if any of the things in desired state is not functioning
then it us up to Kubernetes to relocate them or recreated them in order
to drive our cluster to the desired state until the administrator says
otherwise (e.g. delete the Pod). So, I am going to create a very simple
configuration for out first Pod, a `nginx` yaml configuration and also
follow the examples from kubernetes documentation. Make sure you are
into the `Builds` directory and create our first Pod definition:

```bash
vim nginx.yaml
```

I will create a Pod that has just one single container in it. The Pod will
be named after `nginx` and so does the container also. In the container, I
would like to run `nginx version 1.7.9` and port forward (expose) `TCP 80`.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.7.9
    ports:
    - containerPort: 80
```

Now, if you remember, using `kubectl` we can see what `pods` we have. So, right
now we should not have any pods created yet. Also, if I go to our minion and
look for currently active containers `docker ps` there will be none. So, the
current state is that I have no `pods`, no `services`, no `replication controllers`.
So, now, I am going to launch a Pod within my cluster, and Kubernetes is going
to determine based on the current available worker nodes (minions) where to launch
the container. It is very easy to do:

```bash
kubectl create -f ./nginx.yaml
pod "nginx" created
```

So, if I check for Pod now in my cluster:

```bash
# kubectl get pods
NAME      READY     STATUS    RESTARTS   AGE
nginx     1/1       Running   0          26s
```

As you can see, my Pod called `nginx` is already running. Let us go to the minion
and verify that the container `nginx` is also running:

```bash
[root@drpaneas2 ~]# docker ps
CONTAINER ID        IMAGE                                      COMMAND                  CREATED       
       STATUS              PORTS               NAMES
9fc275cd7597        nginx:1.7.9                                "nginx -g 'daemon off"   About a minute
 ago   Up About a minute                       k8s_nginx.b0df00ef_nginx_default_d4dfb568-45ec-11e7-91f
e-0a35c9149e00_ece8325b
bef1f611ff9b        gcr.io/google_containers/pause-amd64:3.0   "/pause"                 About a minute
 ago   Up About a minute                       k8s_POD.b2390301_nginx_default_d4dfb568-45ec-11e7-91fe-
0a35c9149e00_3ce7af06
```

Now there is our `nginx` container, but there is also another container that is
neccessarry for Kubernetes, which is a `google_container` and it is under `pause`.
If for example we want describe our current Pod, we can do:

```bash
[root@drpaneas1 Builds]# kubectl describe pod nginx
Name:           nginx
Namespace:      default
Node:           centos-minion1/172.31.120.121
Start Time:     Wed, 31 May 2017 10:35:14 +0000
Labels:         <none>
Status:         Running
IP:             172.17.0.2
Controllers:    <none>
Containers:
  nginx:
    Container ID:               docker://9fc275cd7597e315a90826ac4558a5120fdca913bcc46e2895c608d0a0c36
c1c
    Image:                      nginx:1.7.9
    Image ID:                   docker-pullable://docker.io/nginx@sha256:e3456c851a152494c3e4ff5fcc26f
240206abac0c9d794affb40e0714846c451
    Port:                       80/TCP
    State:                      Running
      Started:                  Wed, 31 May 2017 10:35:31 +0000
    Ready:                      True
    Restart Count:              0
    Volume Mounts:              <none>
    Environment Variables:      <none>
Conditions:
  Type          Status
  Initialized   True 
  Ready         True 
  PodScheduled  True 
No volumes.
QoS Class:      BestEffort
Tolerations:    <none>
Events:
  FirstSeen     LastSeen        Count   From                            SubObjectPath           Type Reason                   Message
  ---------     --------        -----   ----                            -------------           --------      ------                  -------
  4m            4m              1       {default-scheduler }                                    NormalScheduled               Successfully assigned nginx to centos-minion1
  4m            4m              1       {kubelet centos-minion1}        spec.containers{nginx}  NormalPulling                 pulling image "nginx:1.7.9"
  4m            4m              2       {kubelet centos-minion1}                                Warning               MissingClusterDNS       kubelet does not have ClusterDNS IP configured and cannot create Pod using "ClusterFirst" policy. Falling back to DNSDefault policy.
  4m            4m              1       {kubelet centos-minion1}        spec.containers{nginx}  NormalPulled                  Successfully pulled image "nginx:1.7.9"
  4m            4m              1       {kubelet centos-minion1}        spec.containers{nginx}  NormalCreated                 Created container with docker id 9fc275cd7597; Security:[seccomp=unconfined]
  4m            4m              1       {kubelet centos-minion1}        spec.containers{nginx}  Normal
Started                 Started container with docker id 9fc275cd7597
```

Some important information here is that you see the docker container `ID`
that is running, the `minion` that is running and its IP Address. So far
there are no `labels`, no `controllers` (e.g. we are not doing any
replication). Just to be clear though:

```bash
# kubectl describe pod nginx | grep 'Node:\|IP:'
Node:           centos-minion1/172.31.120.121 <-- IP of the minion
IP:             172.17.0.2 <-- IP of the container inside the Pod
```

So, can I do anything with the IP of the container?

```bash
# ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
^C
--- 172.17.0.2 ping statistics ---
20 packets transmitted, 0 received, 100% packet loss, time 18999ms
```

The answer is *No*. There is no route externally to that Pod. But what
I can do, is to run other containers within my Pod and as long as they
are in the same host they can see those containers which are defined
within that Pod. So, I am going to run another container: `busybox` image.
This is very minimal installation of Linux running Busybox, that will
allows us to connect/test our `nginx` container.

Instead of creating a proper YAML file, this is just a shortcut, mostly
because this is just for test reasons. So this is going to create a Pod
called `busybox` and create also inside of it a docker container with
the docker image `--image=busybox` and it will be also be
an ephemeral `--restart=Never`. As soon as it will be ready it will be
spawn interactivly here `--tty -i` and it runs in api version 1.

```bash
[root@drpaneas1 Builds]# kubectl run busybox --image=busybox --restart=Never --tty -i --generator=run-
pod/v1
Waiting for pod default/busybox to be running, status is Pending, pod ready: false
Waiting for pod default/busybox to be running, status is Pending, pod ready: false
If you dont see a command prompt, try pressing enter.
/ # 
```

So, now we have two Pods into the same minion:

```bash
[root@drpaneas1 ~]# kubectl get nodes
NAME             STATUS     AGE
centos-minion1   Ready      20h
centos-minion2   NotReady   20h
centos-minion3   NotReady   20h

[root@drpaneas1 ~]# kubectl get pods
NAME      READY     STATUS    RESTARTS   AGE
busybox   1/1       Running   0          14m
nginx     1/1       Running   0          44m
```

This command prompt indicates that I am actually in the Pod `busybox` running a
container in it. The point is that this container can now see the `nginx`

```bash
/ # wget --quiet --output-document - http://172.17.0.2
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>
<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>
<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

In order to delete the `busybox` Pod, first we `exit` and then we issue
the `delete` command:

```bash
[root@drpaneas1 Builds]# kubectl delete pod busybox
pod "busybox" deleted
```

So now if we go back to the minion and look for the busybox container, we will
see that it has been already stopped:

```bash
[root@drpaneas2 ~]# docker ps
CONTAINER ID        IMAGE                                      COMMAND                  CREATED       
      STATUS              PORTS               NAMES
9fc275cd7597        nginx:1.7.9                                "nginx -g 'daemon off"   47 minutes ago
      Up 47 minutes                           k8s_nginx.b0df00ef_nginx_default_d4dfb568-45ec-11e7-91fe
-0a35c9149e00_ece8325b
bef1f611ff9b        gcr.io/google_containers/pause-amd64:3.0   "/pause"                 47 minutes ago
      Up 47 minutes                           k8s_POD.b2390301_nginx_default_d4dfb568-45ec-11e7-91fe-0
a35c9149e00_3ce7af06
```
Now let us also remove the nginx container:

``bash
kubectl delete pod nginx
[root@drpaneas1 Builds]# kubectl delete pod nginx
pod "nginx" deleted
```

I can also `cheat` in a way in order to get access to the services that might be
running on one of my containers. This is by port-forwarding locally to a remote
copy of what happens to be happen within our Pod. So, because we already removed
the `nginx` pod. I am going to create one again:

```bash
[root@drpaneas1 Builds]# kubectl create -f ./nginx.yaml 
pod "nginx" created
[root@drpaneas1 Builds]# kubectl get pods
NAME      READY     STATUS    RESTARTS   AGE
nginx     1/1       Running   0          17s
```

So, now let us port forward it locally:

```bash
[root@drpaneas1 Builds]# kubectl port-forward nginx :80 &
[1] 2512
[root@drpaneas1 Builds]# Forwarding from 127.0.0.1:43873 -> 80
Forwarding from [::1]:43873 -> 80
```

As you can see I am now port forwarding the port 80 from the Pod
`nginx` into my local port `43873`. So, I should be able to do:

```bash
[root@drpaneas1 Builds]# curl http://localhost:43873
Handling connection for 43873
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>
<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>
<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

So now we have a Pod Definition created but one of the things we do not have
is an easy way to talk about what that Pod is all about and what it might
contain. So, I am goin to apply labels and selectors.

### Labels and Selectors

Labels are often reffered to as `tags` in order to differentiate more easily
the Pods running in our system. Because you might run some thousands of Pods
in your system, so it makes sense to apply some common naming scheme to know
how you can sort the information that is available. All that a label does
is to give us a easy-readable plain text to something we can refer to later.
It is a key value that we can also define in our YAML file, and we can also
use it later to get or set information.

```bash
# cp nginx.yaml nginx-pod-label.yaml 
[root@drpaneas1 Builds]# vim nginx-pod-label.yam
```

All we are going to add is a section `labels` and give a name for the `app`
that we are going to run (in that case `nginx`).

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.7.9
    ports:
    - containerPort: 80
```

And create the Pod:

```bash
[root@drpaneas1 Builds]# kubectl create -f nginx-pod-label.yaml 
pod "nginx" created
[root@drpaneas1 Builds]# kubectl get pods
NAME      READY     STATUS    RESTARTS   AGE
nginx     1/1       Running   0          9s
```

Now, what I can do (that I could not do it before) is to ask
for a specific key-value pair, called `nginx`:

```bash
[root@drpaneas1 Builds]# kubectl get pods -l app=nginx
NAME      READY     STATUS    RESTARTS   AGE
nginx     1/1       Running   0          1m
```

Let us create another nginx app:

```bash
[root@drpaneas1 Builds]# cp nginx-pod-label.yaml nginx2-pod-label.yaml 
[root@drpaneas1 Builds]# vim nginx2-pod-label.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: nginx2
  labels:
    app: nginx2
spec:
  containers:
  - name: nginx2
    image: nginx:1.7.9
    ports:
    - containerPort: 80
```

As you can see I had to change `nginx` into `nginx2`. Let us create is also:

```bash
[root@drpaneas1 Builds]# kubectl create -f nginx2-pod-label.yaml 
pod "nginx2" created
[root@drpaneas1 Builds]# kubectl get pods
NAME      READY     STATUS    RESTARTS   AGE
nginx     1/1       Running   0          4m
nginx2    1/1       Running   0          9s
```

This also means that in our minion, we are running now 4 containers:

```bash
[root@drpaneas2 ~]# docker ps
CONTAINER ID        IMAGE                                      COMMAND                  CREATED       
      STATUS              PORTS               NAMES
f9007379f757        nginx:1.7.9                                "nginx -g 'daemon off"   50 seconds ago
      Up 49 seconds                           k8s_nginx2.428f0121_nginx2_default_5f91234f-45f6-11e7-91
fe-0a35c9149e00_ad9421e1
75c528f64f3f        gcr.io/google_containers/pause-amd64:3.0   "/pause"                 50 seconds ago
      Up 49 seconds                           k8s_POD.b2390301_nginx2_default_5f91234f-45f6-11e7-91fe-
0a35c9149e00_1cf67320
b1582754a595        nginx:1.7.9                                "nginx -g 'daemon off"   5 minutes ago 
      Up 5 minutes                            k8s_nginx.b0df00ef_nginx_default_b4dcc3c9-45f5-11e7-91fe
-0a35c9149e00_8810d6a3
e98169846a83        gcr.io/google_containers/pause-amd64:3.0   "/pause"                 5 minutes ago 
      Up 5 minutes                            k8s_POD.b2390301_nginx_default_b4dcc3c9-45f5-11e7-91fe-0
a35c9149e00_e2f1104f
```

So, why would I do it that way? We might have thousands of pods running.
So let us use again the listing for the `app` key-pair-value:

```bash
[root@drpaneas1 Builds]# kubectl get pods -l app=nginx2
NAME      READY     STATUS    RESTARTS   AGE
nginx2    1/1       Running   0          2m
```

This is cool, because now I can only get the description per app, in that
case `nginx2`:

```bash
[root@drpaneas1 Builds]# kubectl describe pods -l app=nginx2
Name:           nginx2
Namespace:      default
Node:           centos-minion1/172.31.120.121
Start Time:     Wed, 31 May 2017 11:43:32 +0000
Labels:         app=nginx2
Status:         Running
IP:             172.17.0.3
Controllers:    <none>
Containers:
  nginx2:
    Container ID:               docker://f9007379f7572c49769164d9b31d9814cd30404ab93c8b73258b672fba449
205
    Image:                      nginx:1.7.9
    Image ID:                   docker-pullable://docker.io/nginx@sha256:e3456c851a152494c3e4ff5fcc26f
240206abac0c9d794affb40e0714846c451
    Port:                       80/TCP
    State:                      Running
      Started:                  Wed, 31 May 2017 11:43:33 +0000
    Ready:                      True
    Restart Count:              0
    Volume Mounts:              <none>
    Environment Variables:      <none>
Conditions:
  Type          Status
  Initialized   True 
  Ready         True 
  PodScheduled  True 
No volumes.
QoS Class:      BestEffort
Tolerations:    <none>
Events:
  FirstSeen     LastSeen        Count   From                            SubObjectPath           Type Reason                   Message
  ---------     --------        -----   ----                            -------------           --------      ------                  -------
  4m            4m              1       {default-scheduler }                                    NormalScheduled               Successfully assigned nginx2 to centos-minion1
  4m            4m              2       {kubelet centos-minion1}                                Warning               MissingClusterDNS       kubelet does not have ClusterDNS IP configured and cannot create Pod using "ClusterFirst" policy. Falling back to DNSDefault policy.
  4m            4m              1       {kubelet centos-minion1}        spec.containers{nginx2} NormalPulled                  Container image "nginx:1.7.9" already present on machine
  4m            4m              1       {kubelet centos-minion1}        spec.containers{nginx2} NormalCreated                 Created container with docker id f9007379f757; Security:[seccomp=unconfined]
  4m            4m              1       {kubelet centos-minion1}        spec.containers{nginx2} Normal
Started                 Started container with docker id f9007379f757
```

This is an easy way to refer to complex infrastucture that I am  virtualizing as
a container in my cluster. So these tags or labels are what is called selectors
when we are getting information or when we apply information to a pod. This is because
we can selectively apply things like deployments to a specific Pod in my environment.
That is all what labels are for, to differentiate key-value pairs in YAML that later
I can identify various parts. We can assign any label we want that we can refer to later.

