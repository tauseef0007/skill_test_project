# 🚀 Track Your Skill — DevOps CI/CD Project

> A full-stack skill tracking web app with a complete production-grade DevOps pipeline.
> Built with Go, Docker, Kubernetes, GitHub Actions, ArgoCD, and Azure.

![App Live](https://img.shields.io/badge/App-Live-brightgreen)
![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-blue)
![K8s](https://img.shields.io/badge/Orchestration-Kubernetes-326CE5)
![ArgoCD](https://img.shields.io/badge/GitOps-ArgoCD-orange)
![Azure](https://img.shields.io/badge/Cloud-Azure-0078D4)

---

## 📋 Table of Contents

- [About](#about)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Pipeline Flow](#pipeline-flow)
- [Project Structure](#project-structure)
- [Local Setup](#local-setup)
- [Docker Setup](#docker-setup)
- [Kubernetes Setup](#kubernetes-setup)
- [CI/CD Pipeline](#cicd-pipeline)
- [ArgoCD GitOps](#argocd-gitops)
- [Live URLs](#live-urls)

---

## 📖 About

**Track Your Skill** is a web application that helps users track their skills and learning sessions. The real focus of this project is the **DevOps pipeline** — from a simple `git push` to a fully automated production deployment.

---

## 🛠 Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | Go (Golang) + Gin Framework |
| Frontend | HTML, CSS, JavaScript + Nginx |
| Database | MySQL (Azure Database for MySQL Flexible Server) |
| Containerization | Docker + Docker Compose |
| Container Registry | DockerHub |
| Orchestration | Kubernetes (Minikube) |
| GitOps | ArgoCD |
| CI/CD | GitHub Actions |
| Cloud | Microsoft Azure (VM + MySQL) |

---

## 🏗 Architecture

```
┌─────────────────────────────────────────────────────┐
│                   Azure VM (Ubuntu)                  │
│                                                      │
│  ┌─────────────── Kubernetes Cluster ─────────────┐ │
│  │                                                 │ │
│  │   ┌──────────────┐    ┌──────────────┐         │ │
│  │   │ frontend Pod │    │ frontend Pod │         │ │
│  │   │  (Nginx)     │    │  (Nginx)     │         │ │
│  │   └──────┬───────┘    └──────┬───────┘         │ │
│  │          │    Service (80)   │                  │ │
│  │          └────────┬──────────┘                  │ │
│  │                   │ /api/*                      │ │
│  │   ┌──────────────┐│   ┌──────────────┐         │ │
│  │   │ backend Pod  ││   │ backend Pod  │         │ │
│  │   │  (Go + Gin)  ││   │  (Go + Gin)  │         │ │
│  │   └──────────────┘│   └──────────────┘         │ │
│  │          Service (8080)                         │ │
│  │                   │                             │ │
│  │   ┌───────────────┴──────────┐                 │ │
│  │   │     ArgoCD               │                 │ │
│  │   │  (GitOps Controller)     │                 │ │
│  │   └──────────────────────────┘                 │ │
│  └─────────────────────────────────────────────────┘ │
│                          │                           │
└──────────────────────────┼───────────────────────────┘
                           │
              ┌────────────▼────────────┐
              │  Azure MySQL Flexible   │
              │  skill-tracker-db       │
              │  (skillpulse database)  │
              └─────────────────────────┘
```

---

## 🔄 Pipeline Flow

```
Developer → git push → GitHub
                          │
                          ▼
              GitHub Actions (CI)
                ├── Checkout code
                ├── Setup Go 1.26
                ├── go mod download
                └── go build ./...
                          │
                          ▼
              GitHub Actions (Docker)
                ├── Login to DockerHub
                ├── Build backend image
                ├── Push tauseef0007/skill-tracker:latest
                ├── Build frontend image
                └── Push tauseef0007/skill-tracker-frontend:latest
                          │
                          ▼
              ArgoCD (GitOps)
                ├── Watches GitHub repo (k8s/ folder)
                ├── Detects changes automatically
                ├── Applies manifests to Kubernetes
                └── Rolling update (zero downtime)
                          │
                          ▼
              Kubernetes Cluster
                ├── 2x backend pods running
                ├── 2x frontend pods running
                ├── Services load balancing
                └── Self-healing enabled
                          │
                          ▼
              App Live! 🎉
```

---

## 📁 Project Structure

```
track_your_skill/
├── backend/                    # Go API Server
│   ├── database/
│   │   └── db.go              # MySQL connection
│   ├── handlers/
│   │   ├── skills.go          # Skills CRUD
│   │   ├── dashboard.go       # Dashboard stats
│   │   └── logs.go            # Learning logs
│   ├── models/
│   │   └── skill.go           # Data models
│   ├── main.go                # Entry point
│   ├── go.mod
│   ├── go.sum
│   └── Dockerfile             # Multi-stage build
├── frontend/                  # Static Web App
│   ├── css/style.css
│   ├── js/app.js
│   ├── index.html
│   └── Dockerfile             # Nginx server
├── k8s/                       # Kubernetes Manifests
│   ├── backend-deployment.yaml
│   ├── frontend-deployment.yaml
│   └── secret.yaml            # DB credentials (gitignored)
├── mysql/
│   └── init.sql               # Database schema
├── .github/
│   └── workflows/
│       └── ci-cd.yml          # GitHub Actions pipeline
├── docker-compose.yml         # Local development
├── nginx.conf                 # Reverse proxy config
└── README.md
```

---

## 💻 Local Setup

### Prerequisites
- Go 1.26+
- Docker & Docker Compose
- MySQL (or use Docker)

### Clone the repo
```bash
git clone https://github.com/tauseef0007/skill_test_project.git
cd skill_test_project
```

### Run with Docker Compose
```bash
# Set environment variables
export DB_HOST=your-db-host
export DB_PORT=3306
export DB_USER=your-user
export DB_PASSWORD=your-password
export DB_NAME=skillpulse

# Start all services
docker compose up -d

# Check status
docker ps
curl http://localhost/health
```

---

## 🐳 Docker Setup

### Build Images
```bash
# Backend
cd backend
docker build -t tauseef0007/skill-tracker:latest .

# Frontend
cd ../frontend
docker build -t tauseef0007/skill-tracker-frontend:latest .
```

### Push to DockerHub
```bash
docker push tauseef0007/skill-tracker:latest
docker push tauseef0007/skill-tracker-frontend:latest
```

### Pull from DockerHub
```bash
docker pull tauseef0007/skill-tracker:latest
docker pull tauseef0007/skill-tracker-frontend:latest
```

---

## ☸️ Kubernetes Setup

### Prerequisites
- Minikube or any K8s cluster
- kubectl configured

### Deploy
```bash
# Create DB secret (update values first!)
kubectl apply -f k8s/secret.yaml

# Deploy backend
kubectl apply -f k8s/backend-deployment.yaml

# Deploy frontend
kubectl apply -f k8s/frontend-deployment.yaml

# Check status
kubectl get pods
kubectl get services
```

### Access the app
```bash
minikube service frontend --url
```

### Useful Commands
```bash
# Scale backend to 4 replicas
kubectl scale deployment backend --replicas=4

# Restart deployment
kubectl rollout restart deployment backend

# View logs
kubectl logs -l app=backend

# Describe pod
kubectl describe pod <pod-name>
```

---

## ⚙️ CI/CD Pipeline

The pipeline has 3 jobs defined in `.github/workflows/ci-cd.yml`:

### Job 1: Build & Test
- Checks out code
- Sets up Go 1.26
- Downloads dependencies
- Builds the Go binary (catches compile errors)

### Job 2: Docker Build & Push
- Runs only if Job 1 passes
- Logs into DockerHub using secrets
- Builds backend Docker image (multi-stage)
- Builds frontend Docker image
- Pushes both images with `latest` and `git-sha` tags

### Job 3: Deploy to VM
- Runs only if Job 2 passes
- SSHs into Azure VM using private key
- Pulls latest Docker images
- Restarts Docker Compose services
- Runs health check

### GitHub Secrets Required
```
DOCKER_USERNAME   → DockerHub username
DOCKER_PASSWORD   → DockerHub access token
VM_HOST           → Azure VM public IP
VM_USER           → VM username
VM_SSH_KEY        → SSH private key
```

---

## 🔁 ArgoCD GitOps

ArgoCD watches the `k8s/` folder in this repo and automatically syncs changes to the Kubernetes cluster.

### Setup
```bash
# Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Create application
argocd app create skill-tracker \
  --repo https://github.com/tauseef0007/skill_test_project \
  --path k8s \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default \
  --sync-policy automated \
  --auto-prune \
  --self-heal
```

### How it works
1. You push a change to `k8s/` folder
2. ArgoCD detects the change within 3 minutes
3. ArgoCD applies the new manifests to Kubernetes
4. Kubernetes does a rolling update (zero downtime)
5. App is updated automatically — no manual steps!

### Access ArgoCD UI
```bash
# Port forward
kubectl port-forward svc/argocd-server -n argocd 8090:443 --address 0.0.0.0

# Get admin password
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath="{.data.password}" | base64 -d
```

---

## 🌐 Live URLs

| Service | URL |
|---------|-----|
| Frontend App | http://4.239.81.116:30080 |
| ArgoCD UI | https://4.239.81.116:8090 |
| Health Check | http://4.239.81.116:30080/health |
| Skills API | http://4.239.81.116:30080/api/skills |
| Dashboard API | http://4.239.81.116:30080/api/dashboard |

---

## 🗺 Roadmap

- [x] Phase 1: Docker + DockerHub
- [x] Phase 2: GitHub Actions CI/CD
- [x] Phase 3: Kubernetes (Minikube)
- [x] Phase 4: ArgoCD GitOps
- [x] Phase 5: Azure MySQL Database
- [ ] Phase 6: Azure AKS (Managed Kubernetes)
- [ ] Phase 7: Helm Charts
- [ ] Phase 8: Monitoring (Prometheus + Grafana)

---

## 👨‍💻 Author

**Tauseef Khan**
- GitHub: [@tauseef0007](https://github.com/tauseef0007)
- Project: [skill_test_project](https://github.com/tauseef0007/skill_test_project)

---

> *"Built with ❤️ while learning DevOps from scratch"*
