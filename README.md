# Car Rental Management System - Kubernetes Deployment

A full-stack Car Rental Management System deployed on Kubernetes with Ansible automation, featuring high availability, scalability, and automated deployment.

## 🏗️ Architecture

- **Frontend**: React + Vite + Nginx (3 replicas)
- **Backend**: Spring Boot + Java 21 (3 replicas)
- **Database**: MySQL 8.0 (with persistent storage)
- **Orchestration**: Kubernetes
- **Automation**: Ansible

## ✨ Features

- ✅ **High Availability**: Multiple replicas for frontend and backend
- ✅ **Zero-Downtime Deployments**: Rolling updates
- ✅ **Health Checks**: Liveness and readiness probes
- ✅ **Resource Management**: CPU and memory limits
- ✅ **Persistent Storage**: MySQL data persistence
- ✅ **Automated Deployment**: Ansible playbooks
- ✅ **Scalability**: Easy horizontal scaling
- ✅ **Containerization**: Docker images for all components

## 📋 Prerequisites

- Kubernetes cluster (v1.20+)
- kubectl configured
- Docker (for building images)
- Ansible (v2.9+) - for automated deployment
- Python Kubernetes library: `pip install openshift kubernetes`

## 🚀 Deployment

### Using Ansible

```bash
# 1. Install Ansible and collections
pip install ansible openshift kubernetes
ansible-galaxy collection install -r ansible/requirements.yml

# 2. Update ansible/inventory.ini with your Kubernetes master IP
# 3. Run deployment
cd ansible
ansible-playbook playbooks/site.yml -i inventory.ini
```

## 📁 Project Structure

```
.
├── carrental-backend/          # Spring Boot backend
│   ├── Dockerfile
│   └── src/
├── carrental-frontend/         # React frontend
│   ├── Dockerfile
│   ├── nginx.conf
│   └── src/
├── k8s/                        # Kubernetes manifests
│   ├── namespace.yaml
│   ├── mysql-*.yaml
│   ├── backend-*.yaml
│   ├── frontend-*.yaml
│   └── ingress.yaml
├── ansible/                    # Ansible playbooks
│   ├── playbooks/
│   │   ├── site.yml
│   │   ├── build-and-push.yml
│   │   └── deploy.yml
│   ├── inventory.ini
│   └── group_vars/
└── README.md                   # This file
```

## 🔧 Configuration

### Kubernetes Resources

- **Namespace**: `carrental`
- **Backend Replicas**: 3
- **Frontend Replicas**: 3
- **MySQL Storage**: 5Gi

### Environment Variables

Backend configuration is managed via ConfigMap:
- Database connection
- JPA settings
- Server port

### Customization

Edit `ansible/group_vars/all.yml` or directly modify Kubernetes manifests in `k8s/` directory.

## 📊 Monitoring & Verification

### Check Pod Status

```bash
kubectl get pods -n carrental
```

### Check Services

```bash
kubectl get svc -n carrental
```

### View Logs

```bash
# Backend
kubectl logs -n carrental -l app=backend --tail=50

# Frontend
kubectl logs -n carrental -l app=frontend --tail=50

# MySQL
kubectl logs -n carrental -l app=mysql --tail=50
```

### Access Application

**Minikube:**
```bash
minikube service frontend -n carrental
```

**Cloud/Production:**
```bash
kubectl get svc frontend -n carrental
# Access via EXTERNAL-IP
```

## 🔄 Scaling

```bash
# Scale backend
kubectl scale deployment backend -n carrental --replicas=5

# Scale frontend
kubectl scale deployment frontend -n carrental --replicas=5
```

## 🧹 Cleanup

```bash
# Delete entire namespace
kubectl delete namespace carrental

# Or delete resources individually
kubectl delete -f k8s/
```

## 📚 Documentation

- [ansible/README.md](ansible/README.md) - Ansible playbooks documentation
- [k8s/README.md](k8s/README.md) - Kubernetes manifests documentation

## 🐛 Troubleshooting

### Pods Not Starting

```bash
kubectl describe pod <pod-name> -n carrental
kubectl logs <pod-name> -n carrental
```

### Image Pull Errors

For local clusters, ensure images are loaded:
```bash
minikube image load carrental-backend:latest
minikube image load carrental-frontend:latest
```

### Database Connection Issues

1. Verify MySQL pod is running
2. Check MySQL logs
3. Verify service endpoint

Check pod logs and events for detailed error information.

## 🔒 Security Considerations

- Database credentials stored in Kubernetes Secrets
- Health endpoints exposed for monitoring
- CORS configured for frontend-backend communication
- Resource limits to prevent resource exhaustion

For production:
- Use private Docker registry
- Enable TLS/SSL
- Implement network policies
- Use external secret management
- Set up regular database backups

## 📝 License

This project is part of a cloud-native modernization initiative.

## 🤝 Contributing

This is a deployment project for the Car Rental Management System. For application development, refer to the main application repository.

## 📞 Support

For deployment issues:
1. Check pod logs and events
2. Review Kubernetes documentation
3. Check application logs for errors
4. Check Kubernetes and Ansible documentation

---

**Built with ❤️ for cloud-native deployment**
