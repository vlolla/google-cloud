# Deployment Manager

Deployment Manager takes this one step further. Deployment Manager is an infrastructure deployment service that automates the creation and management of GCP resources for you. You just specify all the resources needed for your application in a declarative format and deploy your configuration. This deployment can be repeated over and over with consistent results and you can delete a whole deployment with one command or click. The benefit of a declarative approach is that it allows you to specify what the configuration should be and let the system figure out the steps to take. Instead of deploying each resource separately, you specify the set of resources which compose the application or service, allowing you to focus on the application.


- Repeatable deployment Process
- Declarative language
- Focus on the app
- Parallel Deployment
- Template-Driven

# Overview
Deployment Manager is an infrastructure deployment service that automates the creation and management of Google Cloud resources. Write flexible template and configuration files and use them to create deployments that have a variety of Cloud Platform services, such as Cloud Storage, Compute Engine, and Cloud SQL, configured to work together.

In this lab, you create a Deployment Manager configuration with a template to automate the deployment of Google Cloud infrastructure. Specifically, you deploy one auto mode network with a firewall rule and two VM instances, as shown in this diagram:

## Objectives
In this lab, you learn how to perform the following tasks:

Create a configuration for an auto mode network

Create a configuration for a firewall rule

Create a template for VM instances

Create and deploy a configuration

Verify the deployment of a configuration


