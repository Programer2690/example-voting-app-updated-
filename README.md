# Example Voting App

A simple distributed application running across multiple Docker containers, updated to support custom Docker Hub prebuilt images and integrated with Azure DevOps CI/CD workflows.

---

## Architecture Overview

![Architecture diagram](architecture.excalidraw.png)

* **Vote App (`Python/Flask`):** Front-end web app letting users vote between two options.
* **Redis:** In-memory key-value store collecting new votes.
* **Worker (`.NET`):** Background worker consuming votes from Redis and persisting them into Postgres.
* **Database (`Postgres 15`):** Relational database backed by a named Docker volume (`db-data`).
* **Result App (`Node.js`):** Web application displaying live voting results in real time.

---

## Quick Start (Running with Prebuilt Images)

This repository includes a dedicated `docker-compose.images.yml` configured to pull prebuilt container images directly from Docker Hub (`muhammadhussnainali`).

### Prerequisites

* [Docker Desktop](https://www.docker.com/products/docker-desktop) installed and running.

### 1. Pull & Start the Stack

Run the compose file to spin up all 5 microservices:

```shell
docker compose -f docker-compose.images.yml up -d
```

### 2. Access the Applications

* Voting App: http://localhost:8080
* Result App: http://localhost:8081

### 3. Verification

Check the status of running containers:

```shell
docker ps
```

To stop all running services:

```shell
docker compose -f docker-compose.images.yml down
```

---

## Building Multi-Stage Docker Images

The vote service uses a multi-stage Dockerfile (`base`, `dev`, `final`). To ensure application source files are baked directly into the production container, explicitly target the `final` stage during builds:

```shell
# Build production image targeting 'final' stage
docker build --target final -t muhammadhussnainali/examplevotingapp_vote:latest ./vote

# Push updated image to Docker Hub
docker push muhammadhussnainali/examplevotingapp_vote:latest
```

---

## Azure DevOps Integration

This repository is synced with Azure DevOps for continuous integration and source control management.

* **Azure DevOps Org:** `hasnainali2690`
* **Project & Repository Name:** `votingapp`
* **Repository URL:** https://dev.azure.com/hasnainali2690/_git/votingapp

### Syncing Local Repository

If you re-clone or update remote tracking locally, configure the remote URL using:

```shell
git remote set-url origin https://dev.azure.com/hasnainali2690/_git/votingapp
git fetch origin
git status
```

---

## Other Deployment Modes

### Running in Kubernetes

Deploy the manifest specifications stored in the `k8s-specifications/` directory:

```shell
kubectl create -f k8s-specifications/
```

* Vote App: Available on port `31000`
* Result App: Available on port `31001`

To remove deployment resources:

```shell
kubectl delete -f k8s-specifications/
```

### Running in Docker Swarm

Initialize your swarm (if not already running):

```shell
docker swarm init
```

Deploy using the stack specification:

```shell
docker stack deploy --compose-file docker-stack.yml vote
```

---

## Notes

* The voting application accepts one vote per client browser using standard session cookies.
* This repository is designed as a practical learning blueprint for containerization, multi-service network orchestration, and Azure DevOps CI/CD integration.
