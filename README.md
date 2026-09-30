# 🚀 Java Application CI/CD

A complete CI/CD and GitOps implementation for a Java Spring Boot application using Jenkins, Maven, SonarQube, Docker, Amazon ECR, Helm, Argo CD and Amazon EKS.

---

## 📌 Project Overview

This project demonstrates an automated CI/CD workflow where application code is built, tested, analyzed, containerized and deployed to Kubernetes.

The project separates:

- **CI** – Jenkins
- **CD** – Argo CD
- **Source of Truth** – GitHub
- **Container Registry** – Amazon ECR
- **Kubernetes Platform** – Amazon EKS

---

## 🏗️ Architecture

```text
                    GitHub
                       |
                       v
                    Jenkins
                       |
          +------------+-------------+
          |            |             |
        Maven        Tests       SonarQube
          |            |             |
          +------------+-------------+
                       |
                       v
                 Docker Build
                       |
                       v
                  Amazon ECR
                       |
                       v
              Update Helm Values
                       |
                       v
                    GitHub
                       |
                       v
                   Argo CD
                       |
                       v
                  Amazon EKS
                       |
                       v
              Kubernetes Pods
                       |
                       v
             Habit Tracker App
````

---

## 🔄 CI/CD Workflow

```text
GitHub
  ↓
Jenkins
  ↓
Maven Build
  ↓
Unit Test
  ↓
SonarQube Analysis
  ↓
Quality Gate
  ↓
Docker Build
  ↓
Amazon ECR
  ↓
Update Helm values.yaml
  ↓
Git Commit & Push
  ↓
Argo CD
  ↓
Amazon EKS
  ↓
Kubernetes
```

Jenkins performs the **Continuous Integration** process, while Argo CD performs **Continuous Deployment using GitOps**.

---

## 🛠️ Technologies Used

| Technology            | Purpose                           |
| --------------------- | --------------------------------- |
| Java 21 / Spring Boot | Application                       |
| Maven                 | Build & Testing                   |
| GitHub                | Source & Deployment Configuration |
| Jenkins               | CI Pipeline                       |
| SonarQube             | Code Quality                      |
| Docker                | Containerization                  |
| Amazon ECR            | Container Registry                |
| Helm                  | Kubernetes Packaging              |
| Argo CD               | GitOps Deployment                 |
| Amazon EKS            | Kubernetes Platform               |

---

## 📂 Project Structure

```text
Habit-Tracker-Project/
│
├── src/
├── pom.xml
├── Dockerfile
├── .dockerignore
├── Jenkinsfile
├── sonar-project.properties
│
├── habit-tracker/
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/
│       ├── deployment.yaml
│       ├── service.yaml
│       ├── configmap.yaml
│       └── secret.yaml
│
└── README.md
```

---

## 🔧 Jenkins CI Pipeline

The Jenkins Declarative Pipeline automates:

* Source code checkout
* Maven build
* Unit testing
* SonarQube analysis
* Quality Gate validation
* Docker image build
* Image versioning using Git commit SHA
* Push image to Amazon ECR
* Update Helm image tag
* Commit and push deployment configuration


The image version is linked to the Git commit to provide traceability between source code, container image and Kubernetes deployment.

---

## 🐳 Docker

The application uses a multi-stage Docker build.

The production image:

* Uses Java 21 runtime
* Runs on port `8080`
* Uses a non-root `appuser`
* Keeps build dependencies out of the runtime image
* Does not contain application credentials

The generated image is pushed to Amazon ECR using a meaningful version.

---

## ☸️ Kubernetes & Helm

The application is deployed to Amazon EKS using Helm.

The Helm chart manages:

* Deployment
* Service
* ConfigMap
* Secret configuration
* Replica count
* Resource requests and limits
* Liveness and readiness probes
* Rolling update strategy
* Application image version

The application runs with multiple replicas for availability.

---

## 🔄 Argo CD GitOps

Argo CD monitors the deployment configuration stored in GitHub.

When Jenkins updates the image version in `values.yaml`:

```text
Jenkins
   ↓
GitHub
   ↓
Argo CD detects change
   ↓
Automatic Sync
   ↓
Amazon EKS
```

Argo CD is configured with:

* Auto Sync
* Prune Resources
* Self Heal

GitHub acts as the source of truth for the Kubernetes deployment configuration.

---

## 🔄 Deployment & Rollback

Kubernetes rolling updates are used to deploy new application versions.

Deployment status can be verified using : 

``` kubectl rollout status deployment/habit-tracker ```

Deployment history can be viewed using : 

``` kubectl rollout history deployment/habit-tracker ```

A previous version can be restored using : 

``` kubectl rollout undo deployment/habit-tracker ```

---

## 📊 Monitoring & Troubleshooting

Kubernetes and Argo CD are used to monitor application health and troubleshoot deployment issues.

Common commands include:

``` 
kubectl get pods
kubectl get deployment
kubectl get service
kubectl logs <pod-name>
kubectl describe pod <pod-name>
kubectl get pods -o wide
kubectl top pods
```


These checks help identify pod failures, restarts, resource usage, application errors and deployment issues.

---

## 🔐 Security

The project follows basic CI/CD security practices:

* Jenkins Credentials are used for sensitive credentials
* AWS IAM role is used for Jenkins AWS access
* No passwords or tokens are hardcoded
* No sensitive values are committed to GitHub
* Docker container runs as a non-root user
* Kubernetes Secret configuration is used where required
* Jenkins does not directly deploy the application using kubectl
* Kubernetes deployment is managed through GitOps

---

## 📌 Key Features

* Automated Java application build
* Automated unit testing
* SonarQube Quality Gate
* Production Docker image
* Versioned Amazon ECR images
* Reusable Helm deployment
* Kubernetes rolling updates
* Health probes and resource limits
* GitOps deployment with Argo CD
* Automated synchronization to Amazon EKS
* CI/CD separation between Jenkins and Argo CD

---

## 🎯 Final Outcome

The project implements a complete automated delivery pipeline:

**Build → Test → Analyze → Containerize → Push → Update Git → Sync → Deploy**

Jenkins manages the **CI workflow**, while Argo CD manages **GitOps-based CD**, with GitHub acting as the source of truth for the Kubernetes deployment configuration.
