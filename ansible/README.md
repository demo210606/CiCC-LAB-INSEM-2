# Ansible Playbooks for Car Rental Management System Kubernetes Deployment

This directory contains Ansible playbooks to automate the deployment of the Car Rental Management System on Kubernetes.

## Prerequisites

1. **Ansible** (version 2.9 or higher)
   ```bash
   pip install ansible
   ```

2. **Kubernetes cluster** (configured and accessible via kubectl)

3. **Docker** (for building images)

4. **kubectl** (configured to access your Kubernetes cluster)

5. **Python Kubernetes library** (for Ansible k8s module)
   ```bash
   pip install openshift kubernetes
   ```

## Directory Structure

```
ansible/
├── ansible.cfg          # Ansible configuration
├── inventory.ini        # Inventory file with Kubernetes cluster nodes
├── group_vars/
│   └── all.yml          # Variables for all hosts
├── playbooks/
│   ├── site.yml         # Main playbook (orchestrates everything)
│   ├── build-and-push.yml  # Builds and pushes Docker images
│   └── deploy.yml       # Deploys application to Kubernetes
└── README.md            # This file
```

## Configuration

### 1. Update Inventory

Edit `inventory.ini` and replace the placeholder IPs with your actual Kubernetes master node IP:

```ini
[k8s-master]
master ansible_host=192.168.1.100 ansible_user=ubuntu
```

### 2. Configure Variables

Edit `group_vars/all.yml` to customize:
- Docker registry (if using private registry)
- Replica counts
- Resource limits
- Database credentials
- Image tags

### 3. SSH Access

Ensure you have SSH access to the Kubernetes master node. You may need to:
- Set up SSH keys
- Update `ansible.cfg` with the correct `private_key_file` path

## Usage

### Build Docker Images Only

```bash
ansible-playbook playbooks/build-and-push.yml
```

### Deploy to Kubernetes Only

```bash
ansible-playbook playbooks/deploy.yml -i inventory.ini
```

### Full Deployment (Build + Deploy)

```bash
ansible-playbook playbooks/site.yml -i inventory.ini
```

### Deploy with Custom Variables

```bash
ansible-playbook playbooks/deploy.yml -i inventory.ini \
  -e "backend_replicas=5 frontend_replicas=5"
```

## What Gets Deployed

1. **Namespace**: `carrental`
2. **MySQL Database**:
   - Deployment (1 replica)
   - Service (ClusterIP)
   - PersistentVolumeClaim (5Gi)
   - Secret (database credentials)

3. **Backend Application**:
   - Deployment (3 replicas for HA)
   - Service (ClusterIP)
   - ConfigMap (application configuration)

4. **Frontend Application**:
   - Deployment (3 replicas for HA)
   - Service (LoadBalancer)
   - Optional Ingress (if enabled)

## High Availability Features

- **Multiple Replicas**: Backend and frontend run with 3 replicas each
- **Rolling Updates**: Zero-downtime deployments
- **Health Checks**: Liveness and readiness probes
- **Resource Limits**: CPU and memory limits to prevent resource exhaustion
- **Persistent Storage**: MySQL data persisted in PVC

## Troubleshooting

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
# Backend logs
kubectl logs -n carrental -l app=backend --tail=100

# Frontend logs
kubectl logs -n carrental -l app=frontend --tail=100

# MySQL logs
kubectl logs -n carrental -l app=mysql --tail=100
```

### Delete Deployment

```bash
kubectl delete namespace carrental
```

## Notes

- The playbooks assume Docker images are built locally or available in a registry
- For production, use a private Docker registry and update `group_vars/all.yml`
- The Ingress resource is optional and requires an ingress controller (e.g., NGINX Ingress)
- Adjust resource limits based on your cluster capacity

