##  GCP resource hierarchy

 GCP resource hierarchy from the bottom up. All the resources you use, whether they're virtual machines, cloud storage buckets, tables and big query or anything else in GCP are organized into projects. Optionally, these projects may be organized into folders. Folders can contain other folders. All the folders and projects used by your organization can be brought together under an organization node.


Google Cloud Platform resource hierarchy
* Identity and Access Management (IAM)
* Cloud Identity
* Interacting with Google Cloud Platform
* Cloud Marketplace

## Resource hierarchy levels define trust boundaries

Group your resources
according to your
organization structure.
Levels of the hierarchy
provide trust boundaries
and resource isolation.

 Org
 
 Folders
 
 Projects
 
 Resources

All GCP services you use are associated with a project

You can access Cloud Resource Manager in either of the following ways:

● Through the RPC API

● Through the REST API


### Projects have three identifying attributes

 Project ID - Human readable
 Project Name
 Project Number

Folders offer flexible management

    ● Folders group projects under an
    organization.

    ● Folders can contain projects, other
    folders, or both.

    ● Use folders to assign policies.
    Organization Node


### The organization node organizes projects

Notable organization roles:

Organization Policy
Administrator: Broad
control over all cloud
resources

