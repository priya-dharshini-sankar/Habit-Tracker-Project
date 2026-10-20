<div align="center">

# 🚀 Habit Tracker | Java CI/CD & GitOps on Amazon EKS

### Jenkins · Maven · SonarQube · Helm · Argo CD

**A hands-on DevOps project implementing automated CI and GitOps-based CD for a Java Spring Boot application on Amazon EKS, combining build automation, code-quality validation, containerization, Helm-based deployments, and Kubernetes release management.**

![AWS](https://img.shields.io/badge/AWS-EKS%20%7C%20ECR-10212B?style=for-the-badge&logo=amazonwebservices&logoColor=FF9900)
![Jenkins](https://img.shields.io/badge/Jenkins-CI-10212B?style=for-the-badge&logo=jenkins&logoColor=D24939)
![Helm](https://img.shields.io/badge/Helm-Kubernetes-10212B?style=for-the-badge&logo=helm&logoColor=0F1689)
![Argo CD](https://img.shields.io/badge/Argo%20CD-GitOps-10212B?style=for-the-badge&logo=argo&logoColor=EF7B4D)

</div>

---

## 🏗️ CI/CD & GitOps Architecture

```text
GitHub Push
     ↓
Jenkins CI Pipeline
     ↓
Maven Build & Unit Tests
     ↓
SonarQube → Quality Gate
     ↓
Docker Build → Git SHA Image Tag
     ↓
Amazon ECR
     ↓
Update Helm values.yaml
     ↓
Commit & Push to GitHub
     ↓
Argo CD → Automatic Sync
     ↓
Amazon EKS
     ↓
Kubernetes Deployment & Validation
```

**CI:** Jenkins builds, tests, analyzes, and publishes the application.

**CD:** Argo CD synchronizes Git-managed Helm configuration to Amazon EKS.

## 🧩 Application Overview

| Component | Technology | Purpose |
|---|---|---|
| **Application** | Java 21 + Spring Boot | Habit-tracking web application |
| **Build & Testing** | Apache Maven | Dependency management, compilation, unit tests, and JAR packaging |
| **Data Storage** | In-memory | Stores application data without an external database |
| **Runtime** | Docker | Runs the application on port `8080` |

## ⚙️ Engineering Highlights

- **CI Automation:** Built a Jenkins Declarative Pipeline for Maven builds, unit testing, SonarQube analysis, Quality Gate validation, and Docker image publishing.
- **Containerization:** Implemented a multi-stage Docker build using Java 21, a lightweight JRE runtime stage, and non-root container execution.
- **Image Traceability:** Published Docker images to Amazon ECR using Git commit SHA tags to link application versions with source-code revisions.
- **Helm Deployment:** Created a reusable Helm chart with configurable replicas, Services, ConfigMaps, resource limits, and health probes.
- **GitOps Automation:** Configured Jenkins to update Helm image tags in GitHub, allowing Argo CD to automatically synchronize changes to Amazon EKS.
- **Kubernetes Reliability:** Deployed two application replicas with readiness/liveness probes, resource management, and rolling-update configuration.
- **Security Controls:** Used Jenkins Credentials, Kubernetes Secrets, non-root containers, and restricted ServiceAccount access.
- **Deployment & Recovery:** Validated rolling deployments, restored a previous application version, and confirmed Argo CD returned to a healthy, synchronized state.
- **Monitoring & Troubleshooting:** Verified application health, resource usage, pod status, and logs using Kubernetes commands and Argo CD.

## 📊 Documented Results

| Validation | Outcome |
|---|---|
| Maven Build & Unit Tests | Successful — 1 Test Passed |
| SonarQube Quality Gate | Passed |
| Docker Image Build | Successful |
| Amazon ECR Image Publishing | Verified |
| Helm Chart Validation | Passed |
| Kubernetes Deployment | 2/2 Replicas Available |
| Argo CD Synchronization | Synced & Healthy |
| Automated GitOps Deployment | Successful |
| Rolling Update | Successful |
| Application Rollback | Successfully Restored |
| Application Monitoring | Verified |

*Results reflect the completed project implementation, not a currently active deployment.*

## 📚 Project Documentation

### 📄 [Explore the Complete Project Documentation →](./Project%20Documentation.pdf)

*Java & Spring Boot · Jenkins · Maven · SonarQube · Docker · Amazon ECR · Helm · Argo CD · Amazon EKS · GitOps · Rollback*

---

<div align="center">

**Build → Test → Analyze → Publish → Commit → Sync → Deploy**

</div>
