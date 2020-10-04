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



## Module 3 : Virtual Machines



