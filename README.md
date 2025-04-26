# Kubernetes Voting Application

This project demonstrates a complete voting application deployed to Kubernetes, showcasing various Kubernetes concepts and microservices architecture patterns.

## Architecture Overview

The application consists of:

1. **Python Flask Web Application** - Front-end for users to log in, vote, and for admins to view statistics and configure voting options
2. **.NET Worker** - Background service that processes votes from Redis and stores them in PostgreSQL
3. **Node.js Real-time Dashboard** - Shows voting results in real-time using WebSockets
4. **Redis** - Used for temporary storage and message queue
5. **PostgreSQL** - Persistent database for vote storage

## Kubernetes Concepts Demonstrated

- **Deployments**: Managing application lifecycle
- **Services**: Internal and external communication
- **PersistentVolumeClaims**: Database persistence
- **ConfigMaps**: Configuration management
- **Secrets**: Sensitive data storage
- **Ingress**: External access
- **Namespaces**: Logical isolation
- **Resource Limits**: Performance control
- **Health Checks**: Application reliability
- **Multi-container applications**: Microservices architecture

## Directory Structure

```
voting-app/
├── flask-app/                    # Python Flask web application
│   ├── app.py                   # Main Flask application
│   ├── templates/               # HTML templates
│   ├── requirements.txt         # Python dependencies
│   └── Dockerfile               # Docker build file
├── worker/                       # .NET Worker service
│   ├── Program.cs               # Main worker code
│   ├── VoteProcessor.csproj     # .NET project file
│   └── Dockerfile               # Docker build file
├── dashboard/                    # Node.js real-time dashboard
│   ├── app.js                   # Main Node.js application
│   ├── public/                  # Static files
│   ├── package.json             # Node.js dependencies
│   └── Dockerfile               # Docker build file
├── k8s-manifests/                # Kubernetes configuration files
│   ├── namespace.yaml
│   ├── secrets.yaml
│   ├── configmap.yaml
│   ├── redis.yaml
│   ├── postgres.yaml
│   ├── flask.yaml
│   ├── dotnet-worker.yaml
│   ├── nodejs.yaml
│   └── ingress.yaml
├── setup.sh                      # Deployment script
└── README.md                     # This file
```

## Prerequisites

- Docker
- Kubernetes cluster (minikube, kind, or a cloud provider)
- kubectl configured to access your cluster

## Deployment Instructions

1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/voting-app.git
   cd voting-app
   ```

2. Make the setup script executable:
   ```bash
   chmod +x setup.sh
   ```

3. Run the setup script:
   ```bash
   ./setup.sh
   ```

4. Access the application:
   - The Flask web application will be available at the ingress address
   - The real-time dashboard will be available at `http://<ingress-address>/dashboard`

## User Guide

### Normal Users

1. Sign up with a username and password
2. Log in to access the voting page
3. Select an option and submit your vote
4. You can change your vote at any time

### Admin Users

1. Log in with the default admin credentials (username: admin, password: admin123)
2. Access the Statistics page to see:
   - Vote counts by option
   - List of active users
   - Complete voting history
3. Access the Configuration page to:
   - Change the voting title
   - Add, edit, or remove voting options

### Real-time Dashboard

The Node.js dashboard shows voting results in real-time with dynamic updates as votes are cast. It includes:
- Pie chart visualization
- Vote counts and percentages
- Progress bar representation of vote distribution

## Kubernetes Commands

### View all resources in the namespace
```bash
kubectl -n voting-app get all
```

### View application logs
```bash
kubectl -n voting-app logs -f deployment/flask-deployment
kubectl -n voting-app logs -f deployment/dotnet-worker-deployment
kubectl -n voting-app logs -f deployment/nodejs-deployment
```

### Port-forward services for local access
```bash
kubectl -n voting-app port-forward svc/flask-service 5000:5000
kubectl -n voting-app port-forward svc/nodejs-service 3000:3000
```

### Scale applications
```bash
kubectl -n voting-app scale deployment/flask-deployment --replicas=3
```

### Delete the entire application
```bash
kubectl delete namespace voting-app
```

## Production Considerations

For a production environment, consider the following improvements:

1. **Security**:
   - Enable TLS for all services
   - Use proper authentication mechanisms
   - Store secrets in a vault service like HashiCorp Vault

2. **Scalability**:
   - Configure horizontal pod autoscaling
   - Use a Redis cluster instead of a single instance
   - Set up PostgreSQL with replication

3. **Reliability**:
   - Implement proper database backups
   - Set up monitoring with Prometheus and Grafana
   - Configure alerts for service disruptions

4. **CI/CD**:
   - Set up a proper CI/CD pipeline for automated testing and deployment
   - Implement blue/green or canary deployments

## Architecture Diagram

```
                    ┌───────────────┐
                    │     Ingress   │
                    └───────┬───────┘
                            │
              ┌─────────────┴─────────────┐
              │                           │
    ┌─────────▼────────┐       ┌─────────▼────────┐
    │  Flask Web App   │       │  Node.js Dashboard │
    │  (2 replicas)    │       │  (2 replicas)     │
    └─────────┬────────┘       └─────────┬─────────┘
              │                          │
              │                          │
              │                          │
    ┌─────────▼────────┐                 │
    │      Redis       │◄────────────────┘
    │    (cache +      │
    │  message queue)  │
    └─────────┬────────┘
              │
              │
    ┌─────────▼────────┐
    │   .NET Worker    │
    │   (processor)    │
    └─────────┬────────┘
              │
              │
    ┌─────────▼────────┐
    │    PostgreSQL    │
    │   (persistent    │
    │     storage)     │
    └──────────────────┘
```
