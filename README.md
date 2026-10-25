# 🐳 Full-Stack App with Docker, Kubernetes & CI

**A full-stack CRUD application containerized with Docker, built and published by a GitHub Actions CI pipeline, and deployed to a local Kubernetes cluster.**

<p align="left">
  <img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker" />
  <img src="https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white" alt="Kubernetes" />
  <img src="https://img.shields.io/badge/k3d-FFC61C?style=for-the-badge&logo=k3s&logoColor=black" alt="k3d" />
  <img src="https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white" alt="GitHub Actions" />
  <img src="https://img.shields.io/badge/Docker_Hub-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker Hub" />
  <img src="https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB" alt="React" />
  <img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white" alt="PostgreSQL" />
</p>

## 📌 About this project

This project was built for the **Continuous Integration and Delivery** course at FCSE (FINKI), Skopje. It's a simple full-stack CRUD application with a React frontend, a Node.js/Express backend and a PostgreSQL database. The app itself is intentionally simple: the focus is on the DevOps work around it.

The application runs in two ways:

- 🐳 **Docker Compose:** the whole stack runs locally with a single command
- ☸️ **Kubernetes:** the stack is deployed to a local k3d cluster with a namespace, ConfigMap, Secret, a PostgreSQL StatefulSet, Deployments for the frontend and backend, Ingress routing, and a middleware that applies the CORS policy

It also contains a CI pipeline:

- 🔄 **CI pipeline:** every push triggers a GitHub Actions workflow that builds the Docker images and publishes them to Docker Hub


## 🧰 Tech stack

| Area | Technologies |
|---|---|
| Containerization | Docker, Docker Compose |
| CI | GitHub Actions, Docker Hub |
| Orchestration | Kubernetes (k3d), kubectl |
| Application | React, Node.js/Express, PostgreSQL |

## ⚙️ Setup

Make sure you have **Docker Desktop** installed and running, and **k3d** and **kubectl** installed. Ports **3000**, **3001**, **3100** and **3101** must be free.

## 🐳 Dockerization

1. At the project's root, run:

   ```
   docker compose up --build
   ```

   Once the composition has fully started, you can open the frontend at:

   ```
   http://localhost:3000
   ```

   For a backend health check, use:

   ```
   http://localhost:3001/health
   ```

2. To be able to test Kubernetes, stop the application and remove the containers with:

   ```
   docker compose down
   ```

## 🔄 CI Pipeline

1. After cloning the repository, remove the pre-existing remote with:

   ```
   git remote remove origin
   ```

   You can check that it was removed with:

   ```
   git remote -v
   ```

2. Create your own remote GitHub repository and add it as a remote:

   ```
   git remote add origin <your_repository_url>
   ```

3. On GitHub, open your repository and go to **Settings → Secrets and variables → Actions**. Add 2 new repository secrets:

   - `DOCKERHUB_USERNAME`: your Docker Hub username
   - `DOCKERHUB_TOKEN`: an access token generated in Docker Hub

4. Make a small change somewhere in the code or the README, then commit and push it to your repository. Right after that, open the **Actions** tab of your repository and watch the pipeline run. You can also check your Docker Hub repository to see the newly built images.

## ☸️ Kubernetes deployment

1. First, create a cluster:

   ```
   k3d cluster create devops-project-cluster -p "3100:80@loadbalancer" -p "3101:80@loadbalancer" -s 1 -a 1
   ```

2. Then, from the root directory, apply these manifests:

   ```
   kubectl apply -f k8s/namespace.yaml
   kubectl apply -f k8s/configmap.yaml
   kubectl apply -f k8s/secret.yaml
   kubectl apply -f k8s/postgres-statefulset.yaml
   kubectl apply -f k8s/backend-deployment.yaml
   kubectl apply -f k8s/frontend-deployment.yaml
   kubectl apply -f k8s/frontend-ingress.yaml
   kubectl apply -f k8s/backend-ingress.yaml
   ```

3. Next, run:

   ```
   kubectl get ingress -n dev-ops-project
   ```

   Check that the **ADDRESS** column has values. If it doesn't, wait a bit and run the command again until the values appear.

4. Apply the last manifest to enable the CORS policy:

   ```
   kubectl apply -f k8s/backend-middleware.yaml
   ```

5. Finally, so that the DNS name resolves, add this line to your Windows hosts file (`C:\Windows\System32\drivers\etc\hosts`):

   ```
   127.0.0.1 devops-project.local
   ```

With that, the cluster is fully initialized. You can open the frontend at:

```
http://devops-project.local:3100
```

For a backend health check, use:

```
http://localhost:3101/health
```

## 📂 Project structure

```
.
├── frontend/              React application
├── backend/               Node.js/Express API
├── k8s/                   Kubernetes manifests
├── technical-papers/      Project documentation
├── .github/workflows/     GitHub Actions CI pipeline
└── docker-compose.yml     Local Docker Compose stack
```
