## Managing Hybrid Clusters using Kubernetes Engine


Kubernetes is the de-facto standard for container orchestration, and Google Kubernetes Engine (GKE) is a leader in the field of managed Kubernetes offerings. In 2018, Google brought Kubernetes to data centers with a new offering called GKE On-Prem, a certified and managed extension of the cloud-based GKE platform.

Responding to significant early successes and listening to customer needs, Google has expanded its efforts to enable your modernization effort.

Anthos is a modern application management platform announced by Google at Next '19. Anthos provides the tools and technology you need for modern, hybrid, and multi-cloud solutions, all built on the foundations of GKE. Anthos enables several features, including:

Infrastructure provisioning in both cloud and on-premises.

Infrastructure management tooling, security, policies and compliance solutions.

Streamlined application development, service discovery and telemetry, service management, and workload migration from on-premises to cloud.

## Objectives
### Lab Architecture

In this lab, you will learn how to perform the following tasks:

        Understand an installed multi-cluster Kubernetes environment

        Use GKE Hub to authenticate and register a remote Kubernetes cluster using GKE Connect

        Add metadata for the remote cluster

        Review GKE clusters, remote and on Google Cloud, with GKE Dashboard

        Review workloads running in multiple locations across all your clusters


# Task 1. Understand an installed multi-cluster Kubernetes environment

In this task, you will:

    Review the clusters used in this lab

    Enable the necessary APIs

    Download lab files from a GitHub repo

    Run a bootstrap script that installs tools needed to work with the clusters

### Review the clusters used in this lab

In this lab you will use two Kubernetes clusters:

A GKE cluster, named central, in the us-central1 region.

A remote Kubernetes cluster, named remote. This cluster connects to Anthos in the same way a GKE On-Prem cluster, or a cluster on another cloud provider, would connect to Anthos. To avoid installing a VMware environment for this lab, the remote cluster runs on Google Compute Engine,

### Enable the necessary APIs

To access some of the resource types within Google Cloud, you first need to enable a few APIs. These are enabled automatically when you first access a service through the UI or command line. To simplify the process, and reduce user prompts, you explicitly enable the APIs needed.

In Cloud Shell, enable the APIs:

    gcloud services enable \
        cloudresourcemanager.googleapis.com \
        container.googleapis.com \
        gkeconnect.googleapis.com \
        gkehub.googleapis.com \
        serviceusage.googleapis.com \
        anthos.googleapis.com

Output (do not copy)

    Operation "operations/acf.e33064b2-43fc-43dc-9ec6-ad28658a338d" finished successfully.

### Download lab files from a GitHub repo

From Cloud Shell, clone the lab repo.

    git clone -b workshop-v1 https://github.com/GoogleCloudPlatform/anthos-workshop.git anthos-workshop

    cd anthos-workshop

### Run a script that connects Cloud Shell to your clusters
The bootstrap script performs the following tasks:

    Installs the kubectx command-line interface (CLI) tools

    Installs the kops CLI tool

    Updates firewall rules for this Cloud Shell to access the clusters

Run the connect script.

source ./common/connect-kops-remote.sh

The remote cluster that is created will be left detached from Anthos at this time. In the next section, you access the cluster and register it with GKE Hub.

In this task, you enabled remote cluster APIs, installed tools to Cloud Shell including kubectx and istioctl, and established credentials to access central and remote clusters.

# Task 2. Use GKE Hub to authenticate and register a remote Kubernetes cluster using GKE Connect

GKE Hub is a centralized dashboard that allows you to view and manage all of your Kubernetes clusters from one central location. Clusters can be located within Google Cloud, on other cloud vendors, or on-premises.

The mechanism that enables this centralized management is a standard Pod deployed to your clusters called the GKE Connect agent. The agent is responsible for reaching out to the GKE Hub APIs, listening for commands, and providing updates.

GKE Hub requires that the Pods in your cluster are able to reach www.googleapis.com and gkeconnect.googleapis.com, either directly or through a configured proxy server.

In this task, you will:

- Review the existing clusters in Cloud Console

- Check on the remote cluster using kubectl

- Use a Google Cloud service account to create a private key file

- Install Connect Agent on remote cluster

- Create a Kubernetes Service Account (KSA)

- Use GKE Hub to register, authenticate, and connect to the remote cluster

## Review the existing clusters in Cloud Console

Review existing Kubernetes Clusters in Cloud Console from the Navigation menu (Navigation menu) > Kubernetes Engine > Clusters.

You will only see a single GKE cluster named central. You will not see any remote clusters yet.

## Check on the remote cluster using kubectl

As part of bootstrapping this workshop, a Kubernetes cluster was provisioned on Compute Engine. This simulates a cluster running outside of Google Cloud. The procedure that follows can be applied to any Kubernetes cluster that can access the GKE control plane APIs mentioned earlier.

For example, this works equally well on different cloud vendors' conforming Kubernetes clusters or VMWare-based clusters using the GKE On-Prem binaries.

1. Switch kubectl context to remote.
```
kubectx remote
```
2. Verify remote cluster is running.
```
kubectl get nodes
```
output looks like this
```
tudent_01_e42d6871ae4d@cloudshell:~/anthos-workshop (qwiklabs-gcp-01-583be48e2415)$ kubectl get nodes
NAME                        STATUS   ROLES    AGE   VERSION
master-us-central1-b-kknx   Ready    master   21m   v1.18.9
nodes-6l23                  Ready    node     20m   v1.18.9
nodes-pv5l                  Ready    node     20m   v1.18.9
```

## Use a Google Cloud service account to create a private key file

A JSON file containing Google Cloud service account credentials is required to register a cluster. It is recommended that a distinct service account be created for every cluster registration. For labs in Qwiklabs, a service account with a name of format PROJECT-ID@PROJECT-ID.iam.gserviceaccount.com has already been created.

In the following steps, you'll assign the appropriate Cloud IAM roles to the service account, and you'll create a JSON private key containing the service account's credentials.

1. Grant the service account permission to register clusters.

Assign the gkehub.connect Cloud IAM role to the service account.


```
export PROJECT=$(gcloud config get-value project)
export GKE_SA_CREDS=$WORK_DIR/anthos-connect-creds.json
```
Your active configuration is: [cloudshell-8122]

```
gcloud projects add-iam-policy-binding $PROJECT --member="serviceAccount:$PROJECT@$PROJECT.iam.gserviceaccount.com" --role="roles/gkehub.connect"
```


```
- members:
  - serviceAccount:admiral@qwiklabs-services-prod.iam.gserviceaccount.com
  - serviceAccount:qwiklabs-gcp-01-583be48e2415@qwiklabs-gcp-01-583be48e2415.iam.gserviceaccount.com
  - user:student-01-e42d6871ae4d@qwiklabs.net
  role: roles/owner
- members:
  - serviceAccount:qwiklabs-gcp-01-583be48e2415@qwiklabs-gcp-01-583be48e2415.iam.gserviceaccount.com
  - user:student-01-e42d6871ae4d@qwiklabs.net
  role: roles/storage.objectViewer
- members:
  - serviceAccount:qwiklabs-gcp-01-583be48e2415@qwiklabs-gcp-01-583be48e2415.iam.gserviceaccount.com
  - user:student-01-e42d6871ae4d@qwiklabs.net
  role: roles/viewer
etag: BwWwyPkXs-M=
version: 1
```

2. Generate and download a private key to use later.

```
gcloud iam service-accounts keys create $GKE_SA_CREDS --iam-account=$PROJECT@$PROJECT.iam.gserviceaccount.com  --project=$PROJECT

created key [bd964eaa30fa7bf07dcbd479d6b66806ea59c163] of type [json] as [/home/student_01_e42d6871ae4d/anthos-workshop/workdir/anthos-connect-creds.json] for [qwiklabs-gcp-01-583be48e2415@qwiklabs-gcp-01-583be48e2415.iam.gserviceaccount.com]

```

## Install Connect Agent on remote cluster

1. Register the remote cluster using gcloud.

This creates a membership, then installs the Connect Agent.

```
export REMOTE_CLUSTER_NAME="remote"

gcloud container hub memberships register $REMOTE_CLUSTER_NAME --context=$REMOTE_CLUSTER_NAME --service-account-key-file=$GKE_SA_CREDS --project=$PROJECT

 ```

Output (do not copy)

```
Waiting for membership to be created...done.
Created a new membership [projects/qwiklabs-gcp-01-583be48e2415/locations/global/memberships/remote] for the cluster [remote]
Generating the Connect Agent manifest...
Deploying the Connect Agent on cluster [remote] in namespace [gke-connect]...
Deployed the Connect Agent on cluster [remote] in namespace [gke-connect].
Finished registering the cluster [remote] with the Hub.
```

2. Review existing Kubernetes Clusters in Cloud Console from the Navigation menu (Navigation menu) > Kubernetes Engine > Clusters.

Now you see the remote cluster listed, but you need to log in before it's fully connected.

## Create a Kubernetes Service Account (KSA)

To finalize the registration and connection, you need to authenticate and log into the remote cluster through the console. Currently, you can authenticate to an external cluster in one of three ways:

- Token-based authentication: using a Kubernetes Service Account (KSA) with proper RBAC roles to authenticate (using its Secret as a token).
- Basic authentication: using a username and password.
- OpenID Connect.

Most Kubernetes clusters with RBAC enabled have basic authentication disabled.

You can use the KSA token method to authenticate to the remote cluster.

1. Switch kubectl context to remote.

```
kubectx remote
```
2. Create a KSA that you will use to log in.
```
student_01_e42d6871ae4d@cloudshell:~/anthos-workshop (qwiklabs-gcp-01-583be48e2415)$ export KSA=remote-admin-sa
student_01_e42d6871ae4d@cloudshell:~/anthos-workshop (qwiklabs-gcp-01-583be48e2415)$ kubectl create serviceaccount $KSA
serviceaccount/remote-admin-sa created
```

3. Assign it the cluster-admin ClusterRole.
```
kubectl create clusterrolebinding ksa-admin-binding --clusterrole cluster-admin --serviceaccount default:$KSA

Output from kubectl

clusterrolebinding.rbac.authorization.k8s.io/ksa-admin-binding created
```

4. Extract the token from its associated Secret.

```
printf "\n$(kubectl describe secret $KSA | sed -ne 's/^token: *//p')\n\n"
```

Output fromt he secret token

```
eyJhbGciOiJSUzI1NiIsImtpZCI6Ilo4Vmg4WXU2VVZSa19kMW9VVWdmb055VXZxdXE0WFNfb0ZqMzdZTV9FYzgifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6InJlbW90ZS1hZG1pbi1zYS10b2tlbi1rOTl0cCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJyZW1vdGUtYWRtaW4tc2EiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIyOTdkZjkxZC00YzQ2LTRkNDctYjkwYS0xMjY4YjU0ODQ3YzgiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6ZGVmYXVsdDpyZW1vdGUtYWRtaW4tc2EifQ.FTdIjFE9LuzUjx3hVwgal-Z9M0BosK2jQC6_28zM2ug3Io56lnWHEHHdWTyCoe8NiG3QMek8-0Za70GiZPFC2JjQZbDCFuQbAGV-eAAsi_xu2D8xdlExeMCoFwqcVwJe19wEcSy0nW__P59_u7-HK81dXW86BIpsvE2ejNzi3GPxk38QjL5Rn9pyHbQd4UItC0tdbhxXln35wBiMwPhglw2ImTlo62WVNW8eya_C4cqjnVExtrnXKwTIDdbQIyvshm9ipPYnaZHolFkrs4e9RqElBEFYI0ULVbMs_VddQ8g6YTE3VuOdaJDq0JhjzZt1C-su7ZBi00KhdrjnzQkfEg
```

5. Copy the value of the token returned to the clipboard.

In Cloud Shell, select the token output by carefully selecting the text, without capturing any trailing spaces.

## Use GKE Hub to register, authenticate, and connect to the remote cluster

1. Open Kubernetes Clusters in Cloud Console from the Navigation menu (Navigation menu) > Kubernetes Engine > Clusters.

2. Click on login

3. Fill out Log in to cluster form with the above token

4. Kubernetes Clusters in Cloud Console updates on success.


# Task 3. Add metadata for the remote cluster

As a best practice, describe the remote cluster with detailed metadata.

1. From Kubernetes clusters, click Show Info Panel.

2. Select the checkbox next to the remote cluster.

3. Click on + Add label to add a key and value.
    - Add the key location
    - Provide a value of remote

4. Click Save, then click Hide Info panel.


# Task 4. Review GKE clusters, remote and on Google Cloud, with GKE Dashboard

In this task, you inspect the underlying nodes in your remote cluster.

## Review the existing clusters in Cloud Console

1. Click into the remote cluster details in Cloud Console from the Navigation menu (Navigation menu) > Kubernetes Engine > Clusters.

You can see the labels you created and authentication status.

authandlabel.png

2. Click to switch to the Nodes tab of Kubernetes cluster details.

nodes.png


You can see the resources of the nodes (CPU, memory, storage) as well as pods that are running on that node.


# Task 5. Review workloads running in multiple locations across all your clusters

In this task, you review workloads across all clusters using GKE Hub (system workloads)

Review the existing workloads in Cloud Console

The Cloud Console has a view which shows aggregates all the workloads running on all your GKE clusters. With Anthos, you can now see the workloads running on all your remote clusters.

1. Open Kubernetes Workloads in Cloud Console from the Navigation menu (Navigation menu) > Kubernetes Engine > Workloads

Remove the filter: Is system object: False.

workloads.png




Observe that workloads from both clusters are shown. Notice the GKE Connect Agent is running in remote.

2. Click on workloads in both clusters.


Verify that that the same information is shown for workloads, like kube-dns, that run in both central and in the remote cluster.


# Review
In this lab, you used GKE Hub to authenticate and register a non-GKE Kubernetes cluster, set metadata on the cluster, and used GKE Dashboards for cluster and workloads to review information across clusters.

## Next Steps
Read more about registering clusters in the GKE On-Prem overview. https://cloud.google.com/gke-on-prem/docs/concepts/overview



4. How do you securely register and login to remote kubernetes clusters?

Register the remote cluster using GCP Service account Token.


5. What is different about viewing GKE On-Prem clusters using the GCP Console from viewing GKE clusters running on GCP.

All Cluster allow you to review.

