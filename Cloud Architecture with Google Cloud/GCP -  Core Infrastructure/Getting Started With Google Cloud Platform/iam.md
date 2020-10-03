# Identity and Access Management (IAM)

IAM lets administrators authorize who can take action on specific resources. 
An IAM policy has a 

“who” part, a

“can do what” part, and an 

“on which resource” part.

## Who

Who: IAM policies can apply to any of four types of principals


* Service account

* Google account or Cloud Identity user

* Google group

* Cloud Identity or G Suite domain


## Can do what: IAM roles are collections of related permissions 

    InstanceAdmin
         |
        Role
    Service Resource Verb
    compute instances list
    compute instances delete
    compute instances start

## On which resource: Users get roles on specific items in the hierarchy

When you give a user, group, or service account a role on a specific element of the
resource hierarchy, the resulting policy applies to the element you chose, as well as
to elements below it in the hierarchy

# There are three types of IAM roles

* Primitive
* Predifined
* Custom

## IAM primitive roles apply across all GCP services in a project

Primitive roles are broad. You apply them to a GCP project, and they affect all
resources in that project.

IAM primitive roles offer fixed, coarse-grained levels of access

## IAM predefined roles apply to a particular GCP service in a project

IAM predefined roles offer more fine-grained permissions on
particular services

## IAM custom roles let you define a precise set of permissions

IAM custom roles let you define a precise set of permissions
What if you need something even finer-grained? That’s what custom roles permit. A
lot of companies use a “least-privilege” model, in which each person in your
organization the minimal amount of privilege needed to do his or her job. So, for
example, maybe I want to define an “instanceOperator” role, to allow some users to
stop and start Compute Engine virtual machines but not reconfigure them. Custom
roles allow me to do that.


# Service Accounts

Service Accounts control server-to-server interactions

Service Accounts and IAM

● Service accounts
authenticate using keys.

    ○ Google manages keys for
    Compute Engine and App
    Engine.

● You can assign a
predefined or custom
IAM role to the service
account


Example: Service Accounts and IAM

* VMs running component_1 are
    granted Editor access to project_b
    using Service Account 1.
* VMs running component_2 are
    granted objectViewer access to
    bucket_1 using Service Account 2.
* Service account permissions can
    be changed without recreating
    VMs.
