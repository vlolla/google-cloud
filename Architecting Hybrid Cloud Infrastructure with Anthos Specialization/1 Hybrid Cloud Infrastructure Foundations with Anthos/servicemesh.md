# Installing Anthos Service Mesh on Google Kubernetes Engine


## Overview


### Introduction

Istio is an open source framework for connecting, securing, and managing microservices. It can be used with any services, including but not limited to services that are hosted in a Kubernetes cluster. Istio lets you create a network of deployed services with load balancing, service-to-service authentication, monitoring, and more, without requiring any changes in service code.

As one example - in reliable distributed systems, it's common for a system to want to retry a request after a failure, possibly with an exponential backoff delay. There are libraries for Java, Golang and NodeJS that do this. However, employing them within the app means each different app will need to solve that problem independently. The Istio sidecar could do this for the app, automatically.


### Anthos Service Mesh
Anthos Service Mesh (ASM) is powered by Istio. With Anthos Service Mesh, you get an Anthos tested, fully supported, distribution of Istio, letting you create and deploy a service mesh with Anthos GKE, whether your cluster is operating in Google Cloud or on-premises.

You can use included configuration profiles with recommended settings customized for either Google Kubernetes Engine or Anthos GKE on-prem.

Finally, Anthos Service Mesh has a suite of additional features and tools that help you observe and manage secure, reliable services in a unified way:

* Service metrics and logs for HTTP(S) traffic within your mesh's GKE cluster are automatically ingested to Google Cloud.
* Preconfigured service dashboards give you the information you need to understand your services.
* In-depth telemetry lets you dig deep into your metrics and logs, filtering and slicing your data on a wide variety of attributes.
* Service-to-service relationships at a glance help you understand who connects to which service and the services that each service depends on.
* Service Level Objectives (SLOs) provide insights into the health of your services. You can easily define an SLO and alert on your own standards of service health.

Anthos Service Mesh is the easiest and richest way to implement an Istio-based service mesh on your Anthos clusters.

In this lab, you will install Anthos Service Mesh on a GKE cluster.

### Objectives

In this lab, you will learn how to perform the following tasks:

* Provision a cluster on Google Kubernetes Engine (GKE)

* Install and configure Anthos Service Mesh

* Deploy Bookinfo, an Istio-enabled multi-service application

* Enable external access using an Istio Ingress Gateway

* Use the Bookinfo application

* Monitor service performance with the Anthos Service Mesh Dashboard

Lab setup using Qwiklabs

### Activate Google Cloud Shell

Google Cloud Shell is a virtual machine that is loaded with development tools. It offers a persistent 5GB home directory and runs on the Google Cloud. Google Cloud Shell provides command-line access to your GCP resources.

1. In GCP console, on the top right toolbar, click the Open Cloud Shell button.

2. It takes a few moments to provision and connect to the environment. When you are connected, you are already authenticated, and the project is set to your PROJECT_ID. For example:
gcloud is the command-line tool for Google Cloud Platform. It comes pre-installed on Cloud Shell and supports tab-completion.

You can list the active account name with this command:

gcloud auth list

Output:

Credentialed accounts:
 - <myaccount>@<mydomain>.com (active)
Example output:

Credentialed accounts:
 - google1623327_student@qwiklabs.net
You can list the project ID with this command:

gcloud config list project

Output:

[core]
project = <project_ID>
Example output:

[core]
project = qwiklabs-gcp-<< your project id>
Full documentation of gcloud is available on Google Cloud gcloud Overview .

# Task 1. Set up your project

## Verify the SDK configuration
In Cloud Shell, verify your default account is configured.

Verify that the Google Cloud SDK is configured to use your Qwiklabs-generated user account.

```
gcloud config list
```

output

```
student_03_790e94213d8e@cloudshell:~ (qwiklabs-gcp-03-6eac9e4f261a)$ gcloud config list
[component_manager]
disable_update_check = True
[compute]
gce_metadata_read_timeout_sec = 30
[core]
account = student-03-790e94213d8e@qwiklabs.net
disable_usage_reporting = True
project = qwiklabs-gcp-03-6eac9e4f261a
[metrics]
environment = devshell

Your active configuration is: [cloudshell-31112]
student_03_790e94213d8e@cloudshell:~ (qwiklabs-gcp-03-6eac9e4f261a)$
```

2. Update project configuration if needed.

If the SDK does not have the default project set correctly, update the configuration. Replace [project_id] with the name of the project provided in the credential section of the Qwiklabs instructions page.

```
gcloud config set project qwiklabs-gcp-03-6eac9e4f261a
```

## Enable APIs

In Cloud Shell, enable the APIs required to use GKE and Anthos Service Mesh.

```
student_03_790e94213d8e@cloudshell:~ (qwiklabs-gcp-03-6eac9e4f261a)$ gcloud services enable     container.googleapis.com     compute.googleapis.com     monitoring.googleapis.com     logging.googleapis.com     cloudtrace.googleapis.com     meshca.googleapis.com     meshtelemetry.googleapis.com     meshconfig.googleapis.com     iamcredentials.googleapis.com     anthos.googleapis.com     gkeconnect.googleapis.com     gkehub.googleapis.com     cloudresourcemanager.googleapis.com

Operation "operations/acf.b335300d-8705-426e-bf44-aa878faa2b50" finished successfully.
```

## Configure environment variables

Configure environment variables that will be used in the setup and installation commands.

```
export PROJECT_ID=$(gcloud config get-value project)
export PROJECT_NUMBER=$(gcloud projects describe ${PROJECT_ID} \
    --format="value(projectNumber)")
export CLUSTER_NAME=central
export CLUSTER_ZONE=us-central1-b
export WORKLOAD_POOL=${PROJECT_ID}.svc.id.goog
export MESH_ID="proj-${PROJECT_NUMBER}"
```

## Verify sufficient permissions

In Cloud Shell, verify that your user account has the Owner role assigned.
```
gcloud projects get-iam-policy $PROJECT_ID \
    --flatten="bindings[].members" \
    --filter="bindings.members:user:$(gcloud config get-value core/account 2>/dev/null)"

output

---
bindings:
  members: user:student-03-790e94213d8e@qwiklabs.net
  role: roles/owner
etag: BwWwyyPDAsg=
version: 1
---
bindings:
  members: user:student-03-790e94213d8e@qwiklabs.net
  role: roles/viewer
etag: BwWwyyPDAsg=
version: 1
student_03_790e94213d8e@cloudshell:~ (qwiklabs-gcp-03-6eac9e4f261a)$
```

    To complete setup, you need the permissions associated with these roles:
        Project Editor
        Kubernetes Engine Admin
        Project IAM Admin
        GKE Hub Admin
        Service Account Admin
        Service Account key Admin
    If you have the Owner role, you have all these permissions and more, so you're ready to proceed.


# Task 2. Set up your GKE cluster

## Create the cluster

In Cloud Shell, create the GKE cluster.
```
gcloud config set compute/zone ${CLUSTER_ZONE}
gcloud beta container clusters create ${CLUSTER_NAME} \
    --machine-type=n1-standard-4 \
    --num-nodes=4 \
    --workload-pool=${WORKLOAD_POOL} \
    --enable-stackdriver-kubernetes \
    --subnetwork=default \
    --release-channel=regular \
    --labels mesh_id=${MESH_ID}
```

Output (do not copy)

    NAME     ... STATUS
    central  ... RUNNING

It will take several minutes for cluster creation to complete.

## Register the cluster

Connect for Anthos allows you to register any of your Kubernetes clusters to Google Cloud, even clusters running on-premises or on other cloud providers. This enables access to cluster and workload management features in Cloud Console, including a unified user interface to interact with your clusters.

1. In Cloud Shell, check that you have the cluster-admin RBAC role.

        kubectl auth can-i '*' '*' --all-namespaces

Output (do not copy)

        yes

You should have this role because you created the cluster.

If you need cluster-admin, create the clusterrolebinding, replace the variables with appropriate values.

        kubectl create clusterrolebinding [BINDING_NAME] \
            --clusterrole cluster-admin --user [USER]

Create a service account for use by GKE Connect.

        gcloud iam service-accounts create connect-sa

Output (do not copy)

        Created service account [connect-sa].

Assign the gkehub.connect role to the newly created service account.

        gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:connect-sa@${PROJECT_ID}.iam.gserviceaccount.com" \
        --role="roles/gkehub.connect"

Output (do not copy)

        ...
        - members:
        - serviceAccount:connect-sa@qwiklabs-gcp-xx-xxxx.iam.gserviceaccount.com
        role: roles/gkehub.connect
        ...

Create and download a service account key file.

        gcloud iam service-accounts keys create connect-sa-key.json \
        --iam-account=connect-sa@${PROJECT_ID}.iam.gserviceaccount.com

Output (do not copy)

        created key ...

Use the key file to register the cluster.

        gcloud container hub memberships register ${CLUSTER_NAME}-connect \
        --gke-cluster=${CLUSTER_ZONE}/${CLUSTER_NAME}  \
        --service-account-key-file=./connect-sa-key.json

Output (do not copy)

        kubeconfig entry generated for central.
        Waiting for membership to be created...done.
        Created a new membership [...]
        Generating the Connect Agent manifest...
        Deploying the Connect Agent on cluster [central-connect] in namespace [gke-connect]...
        Deployed the Connect Agent on cluster [central-connect] in namespace [gke-connect].
        Finished registering the cluster [central-connect] with the Hub.

As part of registering your cluster, the Connect Agent is deployed to your cluster.

# Task 3. Prepare to install Anthos Service Mesh

1. Initialize your project for service mesh installation.

        curl --request POST \
        --header "Authorization: Bearer $(gcloud auth print-access-token)" \
        --data '' \
        https://meshconfig.googleapis.com/v1alpha1/projects/${PROJECT_ID}:initialize

This command creates a service account for Istio components, among other things.

The command responds with empty curly braces: {}.

2. Download the installation file.

        curl -LO https://storage.googleapis.com/gke-release/asm/istio-1.6.8-asm.0-linux-amd64.tar.gz

3. Download the signature file and verify the signature

        curl -LO https://storage.googleapis.com/gke-release/asm/istio-1.6.8-asm.0-linux-amd64.tar.gz.1.sig
        openssl dgst -verify - -signature istio-1.6.8-asm.0-linux-amd64.tar.gz.1.sig istio-1.6.8-asm.0-linux-amd64.tar.gz <<'EOF'
        -----BEGIN PUBLIC KEY-----
        MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEWZrGCUaJJr1H8a36sG4UUoXvlXvZ
        wQfk16sxprI2gOJ2vFFggdq3ixF2h4qNBt0kI7ciDhgpwS8t+/960IsIgw==
        -----END PUBLIC KEY-----
        EOF
4. Extract the content of the file.

        tar xzf istio-1.6.8-asm.0-linux-amd64.tar.gz
5. Change directories into the installation directory.

        cd istio-1.6.8-asm.0

6. For convenience, add the tools in the /bin directory to your PATH.

        export PATH=$PWD/bin:$PATH

You can now use the istioctl and asmctl tools.

# Task 4. Preparing Resource Configuration Files

When running the istioctl install command, you specify -f istio-operator.yaml on the command line. This file contains information about your project and cluster that is needed to enable the Mesh telemetry and Mesh security features. You need to download the istio-operator.yaml and other resource configuration files and set the project and cluster information.

Install kpt

    sudo apt-get install google-cloud-sdk-kpt

Create a new dierectory for the Anthos Service Mesh package resource configuration files.

    mkdir ${CLUSTER_NAME}
    cd ${CLUSTER_NAME}

Download the Anths Service Mesh package.

    kpt pkg get https://github.com/GoogleCloudPlatform/anthos-service-mesh-packages@1.6.8-asm.9 asm

Use kpt to customize installation settings in the configuration files:

    kpt cfg set asm gcloud.container.cluster ${CLUSTER_NAME}
    kpt cfg set asm gcloud.project.environProjectNumber ${PROJECT_NUMBER}
    kpt cfg set asm gcloud.core.project ${PROJECT_ID}
    kpt cfg set asm gcloud.compute.location ${CLUSTER_ZONE}
    kpt cfg set asm anthos.servicemesh.profile asm-gcp

# Task 5. Install A