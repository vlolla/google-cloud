# Essential Google Cloud Infrastructure: Foundation

## Module 1 : Introduction to GCP

There are four ways you can interact with GCP, and we’ll talk about each in turn.
There’s the Google Cloud Platform Console (or GCP Console), Cloud Shell and the
Cloud SDK, the APIs, and the Cloud Mobile App.


The GCP Console provides a web-based, graphical user interface that you access
through Console.cloud.google.com. For example, you can view your virtual machines
and their details, as shown on the top.
If you prefer to work in a terminal window, the Cloud SDK provides the gcloud
command-line tool. For example, you can list your virtual machines and their details
as shown on the bottom with the “gcloud compute instances list” command.
GCP also provides Cloud Shell, which is a browser-based, interactive shell
environment for GCP that you can access from the GCP Console. Cloud Shell is a
temporary virtual machine with 5 GB of persistent disk storage that has the Cloud
SDK pre-installed.

Lab - https://googlecoursera.qwiklabs.com/focuses/11536865?parent=lti_session


Refer to PDF for more PDF documentation from Course material -- https://storage.googleapis.com/cloud-training/archinfra/v2.1/ondemand/01%20Introduction%20to%20GCP.pdf


Projects:

    gcloud config list


## Module 2 : Virtual networks

In this module, we start by introducing Virtual Private Cloud, or VPC, which is
Google’s managed networking functionality for your Cloud Platform resources. Then
we dissect networking into its fundamental components, which are projects, networks,
subnetworks, IP addresses, routes and firewall rules, along with network pricing.
Next, you will explore GCP’s network structure in a lab by creating networks of many
different varieties and exploring the network relationships between them.
After that, we will look at common network designs.


### VPC Objects
            Projects
            Networks
            Subnetworks
            Regions
            Zones
            IPAddress
            VMs
            Routes
            Firewall rules

A Projects

    ● Associates objects and services
    with billing.

    ● Contains networks (up to 5) that
    can be shared/peered.

A network:

    ● Has no IP address range.

    ● Is global and spans all available
    regions.

    ● Contains subnetworks.

    ● Is available as default, auto, or
    custom.

3 VPC network types

- Default
- Auto Mode
- Custom Mode

### IP Address

VMs can have internal and external IP addresses
## Labs: 
https://googlecoursera.qwiklabs.com/focuses/11538292?parent=lti_session

## Overview

Google Cloud Virtual Private Cloud (VPC) provides networking functionality to Compute Engine virtual machine (VM) instances, Kubernetes Engine containers, and the App Engine flexible environment. In other words, without a VPC network, you cannot create VM instances, containers, or App Engine applications. Therefore, each Google Cloud project has a default network to get you started.

You can think of a VPC network as similar to a physical network, except that it is virtualized within Google Cloud. A VPC network is a global resource that consists of a list of regional virtual subnetworks (subnets) in data centers, all connected by a global wide area network (WAN). VPC networks are logically isolated from each other in Google Cloud.

In this lab, you create an auto mode VPC network with firewall rules and two VM instances. Then, you convert the auto mode network to a custom mode network and create other custom mode networks as shown in the network diagram below. You also test connectivity across networks.

Tasks Notes:

Record the IP address range for the subnets in us-central1 and europe-west1. These will be referred to in the next steps.

	us-central1	mynetwork		
10.128.0.0/20	10.128.0.1			Off	
europe-west1	mynetwork		
10.132.0.0/20	10.132.0.1			Off

34.78.59.3

```
Welcome to Cloud Shell! Type "help" to get started.
Your Cloud Platform project in this session is set to qwiklabs-gcp-04-de3152c6bf70.
Use “gcloud config set project [PROJECT_ID]” to change to a different project.
student_04_592f759130ed@cloudshell:~ (qwiklabs-gcp-04-de3152c6bf70)$ gcloud compute networks create privatenet --subnet-mode=custom
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-04-de3152c6bf70/global/networks/privatenet].
NAME        SUBNET_MODE  BGP_ROUTING_MODE  IPV4_RANGE  GATEWAY_IPV4
privatenet  CUSTOM       REGIONAL

Instances on this network will not be reachable until firewall rules
are created. As an example, you can allow all internal traffic between
instances as well as SSH, RDP, and ICMP by running:

$ gcloud compute firewall-rules create <FIREWALL_NAME> --network privatenet --allow tcp,udp,icmp --source-ranges <IP_RANGE>
$ gcloud compute firewall-rules create <FIREWALL_NAME> --network privatenet --allow tcp:22,tcp:3389,icmp

student_04_592f759130ed@cloudshell:~ (qwiklabs-gcp-04-de3152c6bf70)$ gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-04-de3152c6bf70/regions/us-central1/subnetworks/privatesubnet-us].
NAME              REGION       NETWORK     RANGE
privatesubnet-us  us-central1  privatenet  172.16.0.0/24
student_04_592f759130ed@cloudshell:~ (qwiklabs-gcp-04-de3152c6bf70)$ gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/20
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-04-de3152c6bf70/regions/europe-west1/subnetworks/privatesubnet-eu].
NAME              REGION        NETWORK     RANGE
privatesubnet-eu  europe-west1  privatenet  172.20.0.0/20
student_04_592f759130ed@cloudshell:~ (qwiklabs-gcp-04-de3152c6bf70)$ gcloud compute networks list
NAME           SUBNET_MODE  BGP_ROUTING_MODE  IPV4_RANGE  GATEWAY_IPV4
managementnet  CUSTOM       REGIONAL
mynetwork      CUSTOM       REGIONAL
privatenet     CUSTOM       REGIONAL
student_04_592f759130ed@cloudshell:~ (qwiklabs-gcp-04-de3152c6bf70)$

student_04_592f759130ed@cloudshell:~ (qwiklabs-gcp-04-de3152c6bf70)$ gcloud compute networks subnets list --sort-by=NETWORK
NAME                 REGION                   NETWORK        RANGE
managementsubnet-us  us-central1              managementnet  10.130.0.0/20
mynetwork            us-central1              mynetwork      10.128.0.0/20
mynetwork            europe-west1             mynetwork      10.132.0.0/20
mynetwork            us-west1                 mynetwork      10.138.0.0/20
mynetwork            asia-east1               mynetwork      10.140.0.0/20
mynetwork            us-east1                 mynetwork      10.142.0.0/20
mynetwork            asia-northeast1          mynetwork      10.146.0.0/20
mynetwork            asia-southeast1          mynetwork      10.148.0.0/20
mynetwork            us-east4                 mynetwork      10.150.0.0/20
mynetwork            australia-southeast1     mynetwork      10.152.0.0/20
mynetwork            europe-west2             mynetwork      10.154.0.0/20
mynetwork            europe-west3             mynetwork      10.156.0.0/20
mynetwork            southamerica-east1       mynetwork      10.158.0.0/20
mynetwork            asia-south1              mynetwork      10.160.0.0/20
mynetwork            northamerica-northeast1  mynetwork      10.162.0.0/20
mynetwork            europe-west4             mynetwork      10.164.0.0/20
mynetwork            europe-north1            mynetwork      10.166.0.0/20
mynetwork            us-west2                 mynetwork      10.168.0.0/20
mynetwork            asia-east2               mynetwork      10.170.0.0/20
mynetwork            europe-west6             mynetwork      10.172.0.0/20
mynetwork            asia-northeast2          mynetwork      10.174.0.0/20
mynetwork            asia-northeast3          mynetwork      10.178.0.0/20
mynetwork            us-west3                 mynetwork      10.180.0.0/20
mynetwork            us-west4                 mynetwork      10.182.0.0/20
mynetwork            asia-southeast2          mynetwork      10.184.0.0/20
privatesubnet-us     us-central1              privatenet     172.16.0.0/24
privatesubnet-eu     europe-west1             privatenet     172.20.0.0/20
```

## Lab - Implement Private Google Access and Cloud NAT

https://googlecoursera.qwiklabs.com/focuses/11538674?parent=lti_session

### Overview
In this lab, you implement Private Google Access and Cloud NAT for a VM instance that doesn't have an external IP address. Then, you verify access to public IP addresses of Google APIs and services and other connections to the internet.

VM instances without external IP addresses are isolated from external networks. Using Cloud NAT, these instances can access the internet for updates and patches, and in some cases, for bootstrapping. As a managed service, Cloud NAT provides high availability without user management and intervention.

### Objectives
In this lab, you learn how to perform the following tasks:

- Configure a VM instance that doesn't have an external IP address
- Connect to a VM instance using an Identity-Aware Proxy (IAP) tunnel
- Enable Private Google Access on a subnet
- Configure a Cloud NAT gateway
- Verify access to public IP addresses of Google APIs and services and other connections to the internet

# Module 3 : Virtual Machines



