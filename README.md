# k8s Training - Voting Application

A microservices-based voting application deployed on Kubernetes, demonstrating modern container orchestration and GitOps practices.

## 🎯 Project Overview

This project implements a distributed voting system with real-time results, showcasing microservices architecture, containerization, and Kubernetes deployment patterns.

### Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend      │    │   Backend       │    │   Worker        │
│   (Poll App)    │───▶│   (Result API)  │───▶│   (Java)        │
│   Flask/Python  │    │   Node.js       │    │   Background    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         │                       │                       │
         ▼                       ▼                       ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Redis         │    │   PostgreSQL    │    │   Redis         │
│   (Cache)       │    │   (Database)    │    │   (Queue)       │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## 🏗️ Microservices

### 1. **Frontend (Poll App)**
- **Technology**: Flask (Python)
- **Purpose**: User interface for voting
- **Features**: 
  - Voting interface with multiple options
  - Real-time vote submission
  - Hostname display for load balancing verification

### 2. **Backend (Result API)**
- **Technology**: Node.js/Express
- **Purpose**: API for vote processing and results
- **Features**:
  - RESTful API endpoints
  - Real-time WebSocket connections
  - PostgreSQL integration
  - Vote aggregation and statistics

### 3. **Worker Service**
- **Technology**: Java
- **Purpose**: Background processing
- **Features**:
  - Asynchronous task processing
  - Queue management
  - Data processing and aggregation

### 4. **Data Layer**
- **PostgreSQL**: Primary database for persistent storage
- **Redis**: Caching and session management

## 🚀 Technology Stack

### **Application Layer**
- **Frontend**: Flask (Python), HTML/CSS/JavaScript
- **Backend**: Node.js, Express, Socket.io
- **Worker**: Java (Maven)
- **Database**: PostgreSQL
- **Cache**: Redis

### **Infrastructure Layer**
- **Containerization**: Docker
- **Orchestration**: Kubernetes
- **GitOps**: ArgoCD (planned)
- **Development**: Minikube

## 📁 Project Structure

```
eks-training/
├── apps/                          # Application source code
│   ├── poll/                      # Frontend Flask app
│   │   ├── app.py                 # Main Flask application
│   │   ├── Dockerfile             # Container definition
│   │   └── requirements.txt       # Python dependencies
│   ├── result/                    # Backend Node.js app
│   │   ├── server.js              # Express server
│   │   ├── package.json           # Node.js dependencies
│   │   └── Dockerfile             # Container definition
│   ├── worker/                    # Java worker service
│   │   ├── src/main/java/worker/  # Java source code
│   │   ├── pom.xml               # Maven configuration
│   │   └── Dockerfile            # Container definition
│   └── schema.sql                # Database schema
├── k8s/                          # Kubernetes manifests
│   ├── poll/                     # Frontend deployment
│   ├── result/                   # Backend deployment
│   ├── worker/                   # Worker deployment
│   ├── db/                       # Database deployment
│   └── redis/                    # Redis deployment
├── argocd/                       # GitOps configurations
└── README.md                     # This file
```

## 🛠️ Deployment

### **Prerequisites**
- Docker
- Kubernetes (Minikube for development)
- kubectl

### **Quick Start**

1. **Start Minikube**
   ```bash
   minikube start
   ```

2. **Build and Load Images**
   ```bash
   # Build images locally
   docker build -t poll:latest apps/poll/
   docker build -t result:latest apps/result/
   docker build -t worker:latest apps/worker/
   
   # Load into minikube
   minikube image load poll:latest
   minikube image load result:latest
   minikube image load worker:latest
   ```

3. **Deploy to Kubernetes**
   ```bash
   # Deploy all services
   kubectl apply -f k8s/db/
   kubectl apply -f k8s/redis/
   kubectl apply -f k8s/poll/
   kubectl apply -f k8s/result/
   kubectl apply -f k8s/worker/
   ```

4. **Access the Application**
   ```bash
   # Port forward to access frontend
   kubectl port-forward service/poll-service 5000:5000 -n poll
   # Open http://localhost:5000
   ```

## 🔧 Configuration

### **Environment Variables**

#### **Frontend (Poll App)**
- `REDIS_HOST`: Redis service hostname
- `REDIS_PORT`: Redis service port

#### **Backend (Result API)**
- `POSTGRES_HOST`: Database hostname
- `POSTGRES_PORT`: Database port
- `POSTGRES_USER`: Database username
- `POSTGRES_PASSWORD`: Database password
- `POSTGRES_DB`: Database name

#### **Worker**
- `POSTGRES_HOST`: Database hostname
- `REDIS_HOST`: Redis service hostname

### **Service Communication**

Services communicate using Kubernetes DNS:
- `redis-service.redis.svc.cluster.local:6379`
- `db-service.db.svc.cluster.local:5432`

## 📊 Monitoring and Logs

### **View Logs**
```bash
# Frontend logs
kubectl logs deployment/poll-deployment -n poll

# Backend logs
kubectl logs deployment/result-deployment -n result

# Worker logs
kubectl logs deployment/worker-deployment -n worker

# Database logs
kubectl logs deployment/db-deployment -n db
```

### **Check Pod Status**
```bash
kubectl get pods --all-namespaces
```

## 🎯 Learning Objectives

This project demonstrates:

1. **Microservices Architecture**
   - Service separation and communication
   - Database per service pattern
   - API design and integration

2. **Container Orchestration**
   - Kubernetes deployments and services
   - Namespace isolation
   - Service discovery and load balancing

3. **DevOps Practices**
   - Infrastructure as Code (IaC)
   - GitOps workflows
   - Environment management

4. **Production Readiness**
   - Health checks and monitoring
   - Logging and debugging
   - Scaling and resource management

## 🚀 Next Steps

- [ ] Implement ArgoCD for GitOps
- [ ] Manage DB creation
- [ ] Add monitoring with Prometheus/Grafana
- [ ] Implement CI/CD pipelines
- [ ] Add security policies and network policies
- [ ] Deploy to cloud providers (EKS, GKE, AKS)

## 📚 Resources

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Docker Documentation](https://docs.docker.com/)
- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)
