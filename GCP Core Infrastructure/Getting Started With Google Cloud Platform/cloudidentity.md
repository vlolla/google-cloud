# Cloud Identity

## What can you use to manage your GCP administrative users?

* Gmail accounts and
Google Groups

* Users and groups in
your G Suite domain

* Users and groups in
your Cloud Identity
domain

Many new GCP customers get started by logging into the GCP console with a Gmail
account. To collaborate with their teammates, they use Google Groups to gather
together people who are in the same role. This approach is easy to get started with,
but its disadvantage is that your team’s identities are not centrally managed. For
example, if someone leaves your organization, there is no centralized way to remove
their access to your cloud resources immediately.

GCP customers who are also G Suite customers can define GCP policies in terms of
G Suite users and groups. This way, when someone leaves your organization, an
administrator can immediately disable their account and remove them from groups
using the Google Admin Console.

GCP customers who are not G Suite customers can get these same capabilities
through Cloud Identity. Cloud Identity lets you manage users and groups using the
Google Admin Console, but you do not pay for or receive G Suite’s collaboration
products such as Gmail, Docs, Drive, and Calendar. Cloud Identity is available in a
free and a premium edition. The premium edition adds capabilities for mobile device
management.


## What if you already have a different corporate directory?

Using Google Cloud Directory Sync, your administrators can log in and manage GCP
resources using the same usernames and passwords they already use. 

This tool
synchronizes users and groups from your existing Active Directory or LDAP system
with the users and groups in your Cloud Identity domain. The synchronization is
one-way only; no information in your Active Directory or LDAP map is modified.

Google Cloud Directory Sync is designed to run scheduled synchronizations without
supervision, after its synchronization rules are set up.