# Flask MongoDB Kubernetes Deployment

This project demonstrates the deployment of a Python Flask application integrated with MongoDB on a Kubernetes cluster using Minikube. The repository includes detailed instructions for building and pushing Docker images, Kubernetes configurations, DNS resolution, resource management, design choices, and testing scenarios.


![WhatsApp Image 2024-11-26 at 02 59 54_a5539ef6](https://github.com/user-attachments/assets/e9bae5b2-ada3-47f4-8c8a-8f857ac8ee28)

## Overview

The project deploys a Flask application connected to a MongoDB database within a Kubernetes environment. The setup ensures high availability, scalability, and persistent storage for MongoDB data.

## Features

- **Dockerization**: The Flask application is containerized using Docker.
- **Kubernetes Deployment**: Kubernetes resources are defined to deploy both the Flask application and MongoDB.
- **Horizontal Pod Autoscaling**: Automatically scales the Flask application based on CPU utilization.
- **Persistent Storage**: MongoDB data is stored persistently using Kubernetes Persistent Volumes.

## Project Structure

- **Dockerfile**: Defines the environment for the Flask application.
- **Kubernetes YAML files**: Located in the `k8s/` directory.
  - `flask-deployment.yaml`
  - `flask-service.yaml`
  - `mongodb-statefulset.yaml`
  - `mongodb-service.yaml`
  - `persistent-volume.yaml`
  - `persistent-volume-claim.yaml`
  - `hpa.yaml`

## Dockerfile Description

The `Dockerfile` is used to create a Docker image for the Flask application. It specifies the base image, sets up the working directory, installs dependencies, and runs the application within the container.

### Key Points

- **Base Image**: `python:3.8-slim` was chosen for its balance between minimalism and functionality.
- **Working Directory**: `/app` is set as the working directory.
- **Dependencies**: Installs Python packages listed in `requirements.txt`.
- **Application**: Configures the Flask application to run on port 5000.

### Dockerfile Example

```Dockerfile
FROM python:3.8-slim

WORKDIR /app

COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
COPY . .
CMD ["flask", "run", "--host=0.0.0.0", "--port=5000"]


docker build -t <your-dockerhub-username>/flask-mongodb-app:latest .
docker push <your-dockerhub-username>/flask-mongodb-app:latest


kubectl apply -f k8s/persistent-volume.yaml
kubectl apply -f k8s/persistent-volume-claim.yaml
kubectl apply -f k8s/mongodb-statefulset.yaml
kubectl apply -f k8s/mongodb-service.yaml
kubectl apply -f k8s/flask-deployment.yaml
kubectl apply -f k8s/flask-service.yaml
kubectl apply -f k8s/hpa.yaml


kubectl port-forward svc/flask-service 5000:5000
