# Kubernetes Manifests

This directory contains all Kubernetes manifests for deploying the Car Rental Management System.

## File Structure

```
k8s/
├── namespace.yaml              # Namespace definition
├── mysql-secret.yaml           # MySQL credentials (Secret)
├── mysql-configmap.yaml        # MySQL configuration
├── mysql-pvc.yaml              # PersistentVolumeClaim for MySQL
├── mysql-deployment.yaml       # MySQL deployment
├── mysql-service.yaml          # MySQL service
├── backend-configmap.yaml      # Backend configuration
├── backend-deployment.yaml     # Backend deployment (3 replicas)
├── backend-service.yaml        # Backend service
├── frontend-deployment.yaml    # Frontend deployment (3 replicas)
├── frontend-service.yaml       # Frontend service (LoadBalancer)
└── ingress.yaml                # Ingress (optional)
```

## Deployment Order

1. **Namespace** - Creates the `carrental` namespace
2. **MySQL Secret** - Database credentials
3. **MySQL PVC** - Persistent storage
4. **MySQL Deployment & Service** - Database server
5. **Backend ConfigMap** - Application configuration
6. **Backend Deployment & Service** - API server
7. **Frontend Deployment & Service** - Web interface
8. **Ingress** (optional) - External access with routing

## Quick Deploy

```bash
# Deploy all resources
kubectl apply -f k8s/

# Or deploy in order
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/mysql-secret.yaml
kubectl apply -f k8s/mysql-pvc.yaml
kubectl apply -f k8s/mysql-deployment.yaml
kubectl apply -f k8s/mysql-service.yaml
kubectl apply -f k8s/backend-configmap.yaml
kubectl apply -f k8s/backend-deployment.yaml
kubectl apply -f k8s/backend-service.yaml
kubectl apply -f k8s/frontend-deployment.yaml
kubectl apply -f k8s/frontend-service.yaml
kubectl apply -f k8s/ingress.yaml  # Optional
```

## High Availability

- **Backend**: 3 replicas with rolling updates
- **Frontend**: 3 replicas with rolling updates
- **MySQL**: 1 replica (can be scaled with MySQL replication)

## Resource Requirements

- **Backend**: 512Mi-1Gi memory, 250m-500m CPU per pod
- **Frontend**: 128Mi-256Mi memory, 100m-200m CPU per pod
- **MySQL**: Default MySQL 8.0 requirements + 5Gi storage

## Customization

### Change Replica Count

Edit deployment files:
```yaml
spec:
  replicas: 5  # Change from 3 to 5
```

### Change Resource Limits

Edit deployment files:
```yaml
resources:
  requests:
    memory: "1Gi"
    cpu: "500m"
  limits:
    memory: "2Gi"
    cpu: "1000m"
```

### Use Private Registry

Update image references:
```yaml
image: your-registry.com/carrental-backend:latest
```

## Notes

- Images must be built and available in the cluster
- For local clusters (Minikube/Kind), load images using `minikube image load` or `kind load docker-image`
- Ingress requires an ingress controller (e.g., NGINX Ingress)
- MySQL data persists in PVC - deleting PVC will lose data

