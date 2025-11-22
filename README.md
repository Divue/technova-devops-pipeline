# TechNova DevOps CI/CD Pipeline

End-to-end DevOps pipeline integrating application build, automated testing, static code analysis, artifact management, infrastructure provisioning, and automated deployment to Azure using industry-standard tools.

## Pipeline Overview

1. Build application using Maven  
2. Execute JUnit tests  
3. Perform static code analysis with SonarQube  
4. Package and upload artifact to Nexus  
5. Provision Azure VM using Terraform  
6. Deploy application to VM using Ansible  
7. Run load tests using JMeter  
8. Optionally destroy provisioned infrastructure

## Technology Stack

- Spring Boot (application)
- Maven (build, package)
- JUnit (unit tests)
- SonarQube (SAST)
- Nexus (artifact repository)
- Terraform (Azure VM provisioning)
- Ansible (remote configuration and deployment)
- Jenkins (orchestration)
- Azure (compute environment)
- JMeter (load testing)

## Build and Analysis

The application is built using Maven.  
JUnit tests run during the pipeline.  
SonarQube integrates with Maven to perform code quality and vulnerability checks.

Artifacts are deployed to a Nexus repository for versioned storage.

## Infrastructure Provisioning

Terraform provisions the following resources on Azure:

- Resource Group  
- Virtual Network  
- Subnet  
- Network Security Group  
- Public IP  
- Network Interface  
- Ubuntu Virtual Machine  
- Managed Disks

The VM public IP is exported for use by Ansible during deployment.

## Application Deployment

Ansible performs:

- Installation of required packages  
- Directory setup  
- Removal of old artifacts  
- Downloading latest JAR from repository  
- Java installation  
- Application startup

Deployment is fully automated and triggered within Jenkins.

## Load Testing

JMeter is executed as a separate Jenkins job to validate application performance after deployment.

## Pipeline Execution Flow

1. Code commit triggers Jenkins pipeline  
2. Maven builds and tests  
3. Artifact uploaded to Nexus  
4. Terraform creates Azure VM  
5. Ansible deploys application to the VM  
6. Load tests execute  
7. VM can be destroyed automatically after validation

## Prerequisites

- Jenkins  
- Maven  
- SonarQube  
- Nexus  
- Terraform  
- Ansible  
- Azure subscription  
- JMeter  

## Summary

This project demonstrates a complete DevOps workflow, integrating continuous integration, delivery, infrastructure automation, and deployment, with Azure as the execution environment. It reflects an end-to-end automated pipeline suitable for a cloud-based production setup.

