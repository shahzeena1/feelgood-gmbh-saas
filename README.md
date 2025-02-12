# FEELGOOD-GMBH-SAAS

- Sample code sourced from official documents, stackoverflow, Gen AI resources available online.

- This file aims to explain deployment of nginx, tomcat, artemis using kubernetes in multitenancy setup, how upgrades are handled, authetications are done and automating the deployment using argocd.

### Repository Overview
```
feelgood-gmbh-saas/
├── Namespaces/
│   ├── namespace.yaml
├── Configmaps/
│   ├── central-config.yaml
│   └── nginx-maintenance-configmap.yaml
│   └── maintenance-html-configmap.yaml
├── Deployments/
│   ├── nginx-deployment.yaml
│   ├── tomcat-deployment.yaml
│   ├── artemis-deployment.yaml
├── Jobs/
│   ├── schemagen-job.yaml
├── Services/
│   ├── nginx-service.yaml
├── Ingress/
│   ├── ingress.yaml
├── Argocd/
│   ├── argocd-application.yaml
└── .gitignore
└── README.md
```

# 1. Namespaces/ Directory
namespace.yaml: This file defines the namespaces for your Kubernetes cluster. A namespace is like a virtual cluster within a cluster that isolates the resources (like deployments and services) for each tenant (customer system). Each tenant will get a separate namespace to ensure they are isolated from others.

# 2. Configmaps/ Directory
ConfigMaps are used to store configuration data that containers can access. These files contain important settings for different components of the application.

central-config.yaml: This file contains general configuration settings that are shared across the entire application or multiple components. It includes settings that need to be consistent, like environment variables.

nginx-maintenance-configmap.yaml: Contains the configuration for Nginx when the application is in maintenance mode. It tells Nginx how to serve the maintenance page to users during updates or downtime.

maintenance-html-configmap.yaml: This file holds the HTML content for the maintenance page that Nginx will serve to users while the system is under maintenance.

# 3. Deployments/ Directory
Deployments define how the application components are deployed and updated in the Kubernetes cluster. These files are used to create the containers and manage their lifecycle (e.g., scaling, updating, etc.).

nginx-deployment.yaml: Describes how to deploy the Nginx component in the Kubernetes cluster. It includes information on how many replicas of Nginx to run, which Docker image to use, and what ports to expose.

tomcat-deployment.yaml: Defines the deployment for the Tomcat server, which runs the business logic of the application. This file specifies how Tomcat should be deployed, its resource requirements, and environment variables.

artemis-deployment.yaml: Specifies the deployment of Artemis, the message queue that handles communication between the components (e.g., Nginx, Tomcat). It defines how many replicas to run and configuration for the Artemis container.

# 4. Jobs/ Directory
Jobs define tasks that run once and complete, such as database schema updates or migrations.

schemagen-job.yml: This defines a job that runs a schema generator container for each tenant when Tomcat is updated. The job ensures that the correct database schema changes are applied before the Tomcat server is restarted.

# 5. Services/ Directory
Services define how different components within the Kubernetes cluster can communicate with each other.

nginx-service.yaml: Defines the service for Nginx, which will expose the Nginx container to other parts of the system. It allows other components (like Tomcat and Artemis) to reach Nginx and lets external traffic connect to it.

# 6. Ingress/ Directory
Ingress defines how external traffic (like requests from users) should be routed to the appropriate services inside the cluster.

ingress.yaml: This file configures Ingress to manage external HTTP traffic. It sets up rules to route incoming requests (e.g., based on domain name or path) to the correct service, like Nginx or Tomcat.

# 7. Argocd/ Directory
ArgoCD is a continuous deployment tool for Kubernetes. It automatically syncs the application deployment with the version stored in a Git repository.

argocd-application.yaml: This file tells ArgoCD about the application and where to find the deployment configurations. It links the Git repository to ArgoCD and automatically triggers deployments in Kubernetes whenever changes are made to the repository.

# 8. .gitignore
This file tells Git which files or folders to ignore. It ensures that unnecessary files (like temporary files, build outputs, or sensitive data) are not tracked in the repository.

# 9. README.md
This is the documentation file for the repository. It explains what the project is, how to set it up, and how to deploy it. It's meant to help developers understand how to use and contribute to the project.

### Architecture Diagram

```
+---------------------+
|                     |
|     Nginx           |
| (Static Content +   |
|  Keycloak SSO)      |
|                     |
+---------+-----------+
          |
+---------v-----------+
|                     |
|     Tomcat          |
|  (Core App Logic)   |
|                     |
+---------------------+
          |
+---------v-----------+
|                     |
|   Artemis Queue     |
|   (Message Queue)   |
|                     |
+---------------------+
          |
+---------v-----------+
|                     |
|  External Database  |
|   (Data Storage)    |
|                     |
+---------------------+
```

# Usage
Adding a New Tenant
Edit the k8s/tenants.yaml file and add the necessary tenant information.
The following will happen automatically:
Schema and database setup for the new tenant.
Configuration updates for Tomcat (via ConfigMaps).
Ingress updates to expose the tenant-specific service.
Updating Tomcat
Edit the k8s/deployment.yaml to update the version of Tomcat.
ArgoCD will automatically trigger the deployment.
During the update:
schemagen containers are launched to apply database schema updates.
Traffic is temporarily redirected to the maintenance page served by Nginx until schema updates are complete.
Tomcat is restarted once schema updates are finished.

# Advanced Configuration
Maintenance Page
During updates, Nginx redirects all traffic to a maintenance page. The maintenance page configuration is stored in k8s/maintenance.yaml.

# Schema Generator
Schema updates are handled by the schemagen container, which is automatically triggered when Tomcat is updated. This ensures that the database schema is up-to-date for each tenant before restarting the application.

# Notes
- This setup assumes that the database is managed externally.

- Replicas, high availability, and other advanced features are not covered in this context.

# License
This project is licensed under the MIT License. See the LICENSE.md file for details.

# Contribution
Please don't open any Pull Request as it's a dummy/non-functional code.

# Contact
For any inquiries or support, please contact us at support@feelgoodgmbh.com(dummy email).