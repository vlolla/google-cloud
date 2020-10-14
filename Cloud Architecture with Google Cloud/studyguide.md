1. Designing and planning a cloud solution architecture

    1.1 Designing a solution infrastructure that meets business requirements. Considerations include:

    - Business use cases and product strategy
    - Cost optimization
    - Supporting the application design
    - Integration with external systems
    - Movement of data
    - Design decision trade-offs
    - Build, buy, or modify
    - Success measurements (e.g., key performance indicators [KPI], return on investment [ROI], metrics)
    - Compliance and observability

    1.2 Designing a solution infrastructure that meets technical requirements. Considerations include:

    - High availability and failover design
    - Elasticity of cloud resources
    - Scalability to meet growth requirements
    - Performance and latency

    1.3 Designing network, storage, and compute resources. Considerations include:

Integration with on-premises/multi-cloud environments
Cloud-native networking (VPC, peering, firewalls, container networking)
Choosing data processing technologies
Choosing appropriate storage types (e.g., object, file, RDBMS, NoSQL, NewSQL)
Choosing compute resources (e.g., preemptible, custom machine type, specialized workload)
Mapping compute needs to platform products
1.4 Creating a migration plan (i.e., documents and architectural diagrams). Considerations include:

Integrating solution with existing systems
Migrating systems and data to support the solution
Licensing mapping
Network planning
Testing and proof of concept
Dependency management planning
1.5 Envisioning future solution improvements. Considerations include:

Cloud and technology improvements
Business needs evolution
Evangelism and advocacy
2. Managing and provisioning a solution Infrastructure
2.1 Configuring network topologies. Considerations include:

Extending to on-premises (hybrid networking)
Extending to a multi-cloud environment that may include GCP to GCP communication
Security and data protection
2.2 Configuring individual storage systems. Considerations include:

Data storage allocation
Data processing/compute provisioning
Security and access management
Network configuration for data transfer and latency
Data retention and data life cycle management
Data growth management
2.3 Configuring compute systems. Considerations include:

Compute system provisioning
Compute volatility configuration (preemptible vs. standard)
Network configuration for compute nodes
Infrastructure provisioning technology configuration (e.g. Chef/Puppet/Ansible/Terraform/Deployment Manager)
Container orchestration with Kubernetes
3. Designing for security and compliance
3.1 Designing for security. Considerations include:

Identity and access management (IAM)
Resource hierarchy (organizations, folders, projects)
Data security (key management, encryption)
Penetration testing
Separation of duties (SoD)
Security controls (e.g., auditing, VPC Service Controls, organization policy)
Managing customer-managed encryption keys with Cloud KMS
3.2 Designing for compliance. Considerations include:

Legislation (e.g., health record privacy, children’s privacy, data privacy, and ownership)
Commercial (e.g., sensitive data such as credit card information handling, personally identifiable information [PII])
Industry certifications (e.g., SOC 2)
Audits (including logs)
4. Analyzing and optimizing technical and business processes
4.1 Analyzing and defining technical processes. Considerations include:

Software development life cycle plan (SDLC)
Continuous integration / continuous deployment
Troubleshooting / post mortem analysis culture
Testing and validation
Service catalog and provisioning
Business continuity and disaster recovery
4.2 Analyzing and defining business processes. Considerations include:

Stakeholder management (e.g. influencing and facilitation)
Change management
Team assessment / skills readiness
Decision-making process
Customer success management
Cost optimization / resource optimization (capex / opex)
4.3 Developing procedures to ensure resilience of solution in production (e.g., chaos engineering)

5. Managing implementation
5.1 Advising development/operation team(s) to ensure successful deployment of the solution. Considerations include:

Application development
API best practices
Testing frameworks (load/unit/integration)
Data and system migration tooling
5.2 Interacting with Google Cloud using GCP SDK (gcloud, gsutil, and bq). Considerations include:

Local installation
Google Cloud Shell
6. Ensuring solution and operations reliability
6.1 Monitoring/logging/profiling/alerting solution

6.2 Deployment and release management

6.3 Assisting with the support of solutions in operation

6.4 Evaluating quality control measures