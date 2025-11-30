# Project Concept: DevOps Pipeline for a Spring Boot Application

**Author:** Moetaz Cherni  
**Student ID / Registration Number:** 109463  

---

## 1. Introduction

This document explains the design of an automated CI/CD pipeline created for a simple Spring Boot REST API application.  
The goal of the project is to learn and apply key DevOps principles such as automation, environment separation, security checks, and monitoring — while keeping the setup feasible for a beginner student working on a single machine.

The concept is written for my classmates or a team lead audience — to clearly show the reasoning behind each tooling and architecture decision in a convincing but easy-to-understand way.

- **Infrastructure host:** Local machine  
- **Application source:** GitHub repository (existing Spring Boot REST API for TODO management)  
- **Main aim:** Automatically validate and deploy every new code version safely and with minimal service interruption.

✅ *Justification:* Working locally simplifies learning and testing, but environments are still logically separated to reflect real-world DevOps thinking.

---

## 2. Technology Stack (explained in my own words)

| Layer | Tool | Why I selected it |
|---|---|---|
| **Code Storage & Collaboration** | Git & GitHub | It’s widely used, easy to access from anywhere, and supports pipeline triggers. |
| **Pipeline Automation** | Jenkins | It allows automation of validation and deployment without cloud costs. |
| **Code Quality Check** | SonarQube Analysis | It helps detect mistakes early and improve code structure — even if tests pass. |
| **Security Check** | OWASP Dependency Scan | It identifies known risks inside external libraries without needing SonarQube. |
| **Container Runtime** | Docker | It standardizes the way the app runs and makes deployments reproducible. |
| **Artifact Publishing** | Local Docker Registry + Jenkins artifact storage | This ensures that the exact validated version is deployed, not rebuilt. |
| **Traffic Routing & HTTPS Layer** | Nginx | Central network entry point and handles HTTPS encryption separately from the app. |
| **Monitoring & Visualization** | Spring Boot Actuator + Prometheus + Grafana | Shows app and infrastructure behavior through simple dashboards. |
| **Database Persistence** | MySQL container with isolated volumes | Keeps data safe when app versions are replaced. |

✅ *Justification:* All tools used are free, open-source, and independent, making them suitable for a student project and aligned with the assignment expectations.

---

## 3. Application Lifecycle — CI/CD Pipeline (human explained)

### 3.1 CI Phase (validation before deployment)

Whenever I update the code, Jenkins automatically starts a pipeline that:

1. Takes the new version of the code from GitHub,
2. Builds it to ensure there are no syntax or dependency errors,
3. Runs automated tests to verify the app logic works,
4. Performs a structure and quality analysis,
5. Scans dependencies for publicly known security risks,
6. Creates a standardized container image of the application,
7. Stores both the app package and the container image for later reuse.

✅ *Justification:* This phase ensures that **only safe, working, validated software versions reach deployment**, which prevents runtime failures.

---

### 3.2 Artifact Publication (pipeline output storage)

After CI finishes successfully, the pipeline produces:

- one **application package**,
- one **immutable container image**,  
both stored in a controlled location with version tracking.

✅ *Justification:* Publishing artifacts avoids rebuilding during deployment and ensures reproducibility — which was a professor's concern in feedback.

---

### 3.3 CD Phase (deployment to the right environment)

Jenkins then deploys the software by:

1. Understanding which environment should receive the new version (dev or prod),
2. Starting the **new version first**, without stopping the currently running one,
3. Checking that it works correctly using internal health information,
4. Switching the user traffic to the new version,
5. Stopping the old version only *after* confirming the new one works.

✅ *Justification:* Deploying “green before stopping blue” greatly reduces downtime and shows a simple form of continuity strategy suitable for a beginner student.

---

## 4. Environments and Their Separation (answering professor’s expectations)

Even though everything runs locally, I separated environments **logically**, because physically it's not possible on one machine for this student-level project.

| Environment | Role | Isolation Method |
|---|---|---|
| **CI System** | Jenkins pipeline runtime | Independent container, not stopped during app deployment. |
| **Development (dev)** | Tests new features | Different internal network, different app access address, isolated database. |
| **Production (prod)** | Final stable version | Different network, separate data and credentials from dev, only receives validated main branch versions. |

✅ *Justification:*  
- **dev** gets early/tested versions,  
- **prod** gets only stable and validated versions,  
both separated by **workflow rules, access addresses, and isolated data spaces**, which is what the professor asked to clarify.

---

## 5. Networking, TLS, Domains (non-technical explanation)

- **Nginx receives all user requests** as a front service.
- Users connect securely using **HTTPS encryption (TLS)**.
- Nginx only handles decryption, not the application.
- After decrypting, Nginx forwards traffic internally to the correct environment instance (dev or prod).
- Two local simulated domains are used and mapped inside my computer using a simple resolver file, to distinguish environments clearly.

Examples of domains used in my concept:
- `dev.springapp.local`
- `prod.springapp.local`

✅ *Justification:* Using local domains helps me and my team think in a **multi-environment mindset** even locally, without depending on real internet DNS.

---

## 6. Monitoring and Logging (observability concept)

### Monitoring
Internal application metrics and behavior are collected and shown through dashboards focusing on:

- application availability,
- speed of request responses,
- error occurrence frequency,
- memory and CPU usage of runtime environments,
- Jenkins build stability,
- storage health for container images and database persistence.

### Logging
- The application prints logs normally without complex tooling,
- The container runtime captures these logs,
- Logs remain accessible for troubleshooting.

✅ *Justification:* Observability is included to match assignment requirements and helps detect failures or bottlenecks quickly.

---

## 7. Deployment and Continuity Strategy (without going too technical)

To ensure minimal interruption:
- The pipeline always **starts the new version before stopping the old one,**
- Traffic is redirected only after Jenkins confirms the new software version works,  
using internal health indicators to judge if it is safe.

✅ *Justification:* This is a beginner-appropriate version of a **service continuity strategy** (similar to blue/green), showing understanding without complex orchestration.

---

## 8. TLS Position and DNS (professor’s networking questions answered in my student logic)

| Networking Concern | My Concept Answer |
|---|---|
| **TLS Termination Location** | Managed centrally by Nginx at pipeline front |
| **DNS Configuration** | Simulated locally via internal domain mapping file |
| **CI vs Runtime SPOF** | CI infrastructure isolated logically from runtime app dependencies |
| **Used Domains** | `.local` simulated domains |
| **Target Environments** | `dev` and `prod`, logically isolated |

✅ *Justification:* This directly answers professor’s feedback questions non-technically, proving comprehension.

---

## 9. Limitations (showing awareness like a student)

| Limitation | Why it exists |
|---|---|
| All services run on one machine | Acceptable for learning but not real HA |
| Docker daemon is a SPOF risk | Real-world would separate CI and runtime across multiple hosts |
| No real public DNS or certificates | Environments simulated locally for learning |

✅ *Justification:* I acknowledge these to show understanding of architecture risks, not ignorance.

---

## 10. Conclusion

This concept shows that I understand the full pipeline lifecycle:

**Trigger → CI validation → Artifact publication → CD deploy → Traffic routing via Nginx → Monitoring & logs → Environment separation**

Even as a beginner, I ensured that:
- CI tools and runtime environments are **not dangerously coupled**,
- deployment versions are **validated before production delivery**,
- environments are **logically isolated**,
- user traffic is **protected with TLS at proxy level**,
- pipeline results are **published and reused immutably**,
- monitoring is included for **visibility and troubleshooting**.


---
