# Concept Document: DevOps Pipeline for a Spring Boot Application
**Author:** Moetaz Cherni  
**Registration Number:** 109463  

---

## 1. Introduction
This document describes the concept and architecture of a fully automated CI/CD pipeline for a Spring Boot application.  
Unlike cloud-based deployments, this setup performs both Continuous Integration (CI) and Continuous Deployment (CD) directly on the same local development machine using Docker containers.

The goal is to ensure a reproducible, secure, and efficient workflow using only free and open-source tools.

---

## 2. Technology Stack

| Component               | Tool                      | Purpose                                               |
|------------------------|---------------------------|-------------------------------------------------------|
| Version Control        | Git + GitHub              | Source hosting, collaboration, CI triggers.           |
| Build & Tests          | Maven                     | Compilation, dependency management, unit tests.       |
| Code Quality           | SonarQube                 | Static analysis and code quality validation.          |
| Dependency Security    | OWASP Dependency-Check    | CVE detection in application dependencies.            |
| Containerization       | Docker                    | Standardized runtime environment for CI & production. |
| CI/CD Automation       | Jenkins (local runner)    | Automates build, scans, packaging, deployment.        |
| Hosting Environment    | Local Machine (Docker)    | Runs CI processes and production containers.          |
| Database               | MySQL (Docker)            | Persistent storage via Docker volumes.                |

---

## 3. Application Lifecycle

All automation steps occur on the same machine using a Jenkins self-hosted runner.

### **1. Continuous Integration (CI)**
Triggered by a GitHub push:

1. Checkout repository  
2. Build and test using Maven  
3. Perform static code analysis with SonarQube  
4. Scan dependencies with OWASP Dependency-Check  
5. Build Docker image locally  
6. Tag and store the image in the local Docker repository  

---

### **2. Continuous Deployment (CD)**
After successful CI:

1. Stop and remove any running application container  
2. Deploy the new application container using the latest image  
3. Maintain database state via persistent Docker volumes  

---

## 4. Infrastructure and Provisioning

### **Local Hosting Environment**
All components run locally:

- Docker Engine for application, database, and supporting services  
- Jenkins self-hosted runner  
- No SSH required — Jenkins interacts directly with Docker  

### **Provisioning Steps**
1. Install Docker and Docker Compose  
2. Create `docker-compose.yml` for:  
   - Spring Boot container  
   - MySQL container with persistence  
   - (optional) SonarQube container  
3. Install and configure Jenkins runner  
4. Configure GitHub/Jenkins pipelines to:  
   - run CI  
   - build & publish Docker images  
   - restart containers during deployment  

---

## 5. Environment Model

| Environment Type         | Purpose                                           |
|--------------------------|---------------------------------------------------|
| Local CI Runner          | Builds, tests, scans, and analyzes code           |
| Local Docker Environment | Runs application and MySQL database persistently  |

---

## 6. Architectural Overview

The pipeline operates fully on the local machine:

- Developer pushes code to GitHub  
- Jenkins triggers the pipeline  
- CI builds, analyzes, scans, and packages the application  
- Docker image is rebuilt and deployed locally  

*(Insert diagram here if needed)*

---

## 7. Rationale for Technology Choices
- **GitHub:** easy syncing and CI triggers  
- **Docker:** consistent runtime across environments  
- **SonarQube & OWASP:** improves software quality and security  
- **Local Jenkins Runner:** ideal for local build + deploy environments  

---

## 8. Persistence Layer
MySQL runs inside a Docker container with persistent volumes.  
The application can be redeployed without losing any database data.

