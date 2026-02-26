# Site Reliability Engineering Interview Guidelines

Welcome to the Site Reliability Engineer take-home coding challenge! 

This project is designed to evaluate your skills in `productionizing applications`, `managing container orchestration`, and ensuring the `reliability` and `observability` of services in a `Kubernetes production environment`.

## Overview:

Your goal is to create a containerized deployment strategy using Docker and deploy the application(s) on Kubernetes (K8s) with Helm charts.

Make sure you to include a minimal CI/CD pipeline to help you out.

The application to be containerized is bundled in this repository:
- [Backend Service](../backend/README.md) - Backend Application (BE)

This assignment will give us insight into your technical expertise, problem-solving skills, and ability to work with modern infrastructure tools.

#### Fork the repository and clone it locally
- https://github.com/Tekmetric/interview.git

#### Import project into IDE
- Project root is located in `sre` folder

#### After finishing the goals listed below create a PR

- if you've forked the repository, create a PR from your fork to `Tekmetric/interview` repository
# Goals
1. Create a Dockerfile to containerize the application.
2. Create a minimal CI/CD pipeline to demonstrate building and pushing Docker images to a container registry (e.g., Docker Hub, AWS ECR, etc.).
3. Develop a Helm chart to deploy the application on a K8s or K3s cluster.
   1. Make sure that whatever you implement in the Helm chart is `as close as possible to production-ready`.
4. Add relevant observability
5. Provide clear instructions on how to build and deploy the application, including any prerequisites.

# Considerations
This is an open-ended exercise for you to showcase what you know!

### Submitting your coding exercise
Once you have finished the coding exercise please create a PR into Tekmetric/interview

### Excited to see what you build! 🚀

For the interview, make sure you have the following ready:
- An IDE with your submission code
- A running K8s or K3s cluster (local or cloud-based) to demonstrate your Helm chart deployment
- Make sure you can explain your design choices and answer any questions related to the exercise.
- You will have only 10 minutes to showcase your submission during the interview, so please be concise and focus on the key aspects of your implementation.

# Implementation Notes
- Backend containerization lives in `../backend/Dockerfile`.
- CI/CD pipeline is in `../.github/workflows/ci.yml` and pushes to Docker Hub.
- Helm chart is located at `./charts/backend`.
- Observability is provided via application logs to stdout and a health endpoint at `/actuator/health`.

# Prerequisites (Local Kubernetes via Docker Desktop)
- Docker Desktop with Kubernetes enabled
- NGINX Ingress Controller enabled (Docker Desktop: Settings -> Kubernetes -> Enable Kubernetes, then enable the built-in ingress or install NGINX ingress)
- kubectl
- Helm 3
- Docker Hub account (for pushing/pulling images)

# Build and Run (Local)
## Option A: Build locally and deploy (no registry push needed for Docker Desktop)
- From repository root:
- `docker build -t interview-backend:local ./backend`
- `helm install interview-backend ./sre/charts/backend --set image.repository=interview-backend --set image.tag=local`
- Add a hosts entry: `127.0.0.1 interview-backend.local`
- `curl http://interview-backend.local/api/welcome`
- `curl http://interview-backend.local/actuator/health`

## Option B: Push to Docker Hub and deploy
- `docker login`
- `docker build -t <dockerhub-username>/interview-backend:dev ./backend`
- `docker push <dockerhub-username>/interview-backend:dev`
- `helm install interview-backend ./sre/charts/backend --set image.repository=<dockerhub-username>/interview-backend --set image.tag=dev`
- Add a hosts entry: `127.0.0.1 interview-backend.local`
- `curl http://interview-backend.local/api/welcome`
- `curl http://interview-backend.local/actuator/health`

# CI/CD (Docker Hub)
- Set repository secrets:
- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN`
- On push to `main`, the pipeline builds and pushes tags:
- `sha-<git-sha>`
- `latest`
