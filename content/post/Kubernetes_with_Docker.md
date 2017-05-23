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

