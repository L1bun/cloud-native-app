# Cloud Native System Monitor

![Python](https://img.shields.io/badge/python-3.10-yellow)
![Flask](https://img.shields.io/badge/flask-2.x-lightgrey)
![Docker](https://img.shields.io/badge/docker-ready-blue)
![Kubernetes](https://img.shields.io/badge/kubernetes-deployed-blue)
![AWS ECR](https://img.shields.io/badge/AWS-ECR-orange)
![AWS ECS](https://img.shields.io/badge/AWS-ECS-orange)
![License](https://img.shields.io/badge/license-MIT-green)

A real-time system monitoring dashboard built with Python and Flask, containerized with Docker, and deployed to AWS using ECR and ECS via Kubernetes. Displays live CPU and memory usage with interactive Plotly gauge charts and automatic alerting when thresholds are breached.

---

## Dashboard Preview

> Real-time CPU and memory gauges update automatically. Alerts fire when usage exceeds 80%.

*(Add a screenshot here — run the app locally and take a screenshot of the dashboard)*

---

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                   Local Development                  │
│                                                     │
│   app.py (Flask)  ──▶  Dockerfile  ──▶  Docker Image│
└─────────────────────────────┬───────────────────────┘
                              │  docker push
                              ▼
                    ┌─────────────────┐
                    │    AWS ECR      │
                    │ (Image Registry)│
                    └────────┬────────┘
                             │  kubectl apply
                             ▼
                    ┌─────────────────┐
                    │   Kubernetes    │
                    │  deployment.yaml│
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │    AWS ECS      │
                    │  (Running App)  │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │  Browser :5000  │
                    │  CPU & Memory   │
                    │  Live Gauges    │
                    └─────────────────┘
```

---

## Features

- **Real-time CPU monitoring** — live gauge updates every refresh
- **Real-time memory monitoring** — shows used vs available memory
- **Threshold alerts** — automatic warning when CPU or memory exceeds 80%
- **Interactive charts** — built with Plotly for smooth visualization
- **Cloud deployed** — pushed to AWS ECR, orchestrated via Kubernetes on ECS
- **Fully containerized** — runs identically in local Docker or cloud

---

## Tech Stack

| Technology | Role |
|-----------|------|
| **Python / Flask** | Web backend serving the monitoring dashboard |
| **psutil** | System metrics collection (CPU, memory) |
| **Plotly** | Interactive real-time gauge charts |
| **Docker** | Containerization and image build |
| **AWS ECR** | Private container image registry |
| **AWS ECS** | Managed container deployment on AWS |
| **Kubernetes** | Container orchestration and deployment manifests |

---

## Project Structure

```
cloud-native-app/
├── app.py              # Flask app — collects metrics and renders dashboard
├── ecr.py              # AWS ECR helper — builds and pushes image to registry
├── dockerfile          # Container build instructions
├── requirements.txt    # Python dependencies
├── deployment.yaml     # Kubernetes Deployment + Service manifest
├── templates/
│   └── index.html      # Dashboard UI with Plotly gauges
└── README.md
```

---

## Getting Started

### Prerequisites

- Python 3.10+
- Docker installed and running
- AWS CLI configured (`aws configure`)
- kubectl + an active Kubernetes cluster (or Minikube for local)

---

### Option 1 — Run locally with Docker

```bash
# Clone the repo
git clone https://github.com/L1bun/cloud-native-app.git
cd cloud-native-app

# Build the image
docker build -t cloud-native-app .

# Run the container
docker run -p 5000:5000 cloud-native-app
```

Open `http://localhost:5000`

---

### Option 2 — Deploy to AWS ECR + ECS

```bash
# Authenticate Docker to ECR
aws ecr get-login-password --region <your-region> | \
  docker login --username AWS --password-stdin <your-account-id>.dkr.ecr.<your-region>.amazonaws.com

# Build and push using the helper script
python ecr.py

# Apply Kubernetes manifests
kubectl apply -f deployment.yaml

# Forward the service port
kubectl port-forward service/flask-service 5000:80
```

Open `http://localhost:5000`

---

### Option 3 — Run with Kubernetes locally (Minikube)

```bash
# Start Minikube
minikube start

# Apply manifests
kubectl apply -f deployment.yaml

# Access the service
kubectl port-forward service/flask-service 5000:80
```

---

## How It Works

1. `app.py` uses `psutil` to read CPU and memory stats from the host system
2. Flask renders `index.html`, injecting the live metrics as JSON
3. Plotly builds gauge charts in the browser from the metrics data
4. If CPU > 80% or memory > 80%, a red alert banner appears on the dashboard
5. The Docker image packages everything into a portable container
6. `ecr.py` automates tagging and pushing the image to AWS ECR
7. `deployment.yaml` defines the Kubernetes Deployment and Service to run the container on ECS

---

## Author

**Libun Gaade** — DevOps Engineer  
[LinkedIn](https://www.linkedin.com/in/libungaade) · [GitHub](https://github.com/L1bun)
