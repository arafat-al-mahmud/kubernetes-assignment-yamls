# Kubernetes Deployment

This repository contains Kubernetes manifests for deploying a full-stack Student Management System consisting of:
- **Frontend**: Vite-based React application (ostad-ui)
- **Backend**: Node.js API server (ostad-server)
- **Database**: MongoDB
- **Admin Panel**: Mongo Express

## Project Structure

All resources are deployed in the `arafat-al-mahmud-ns` namespace.

## YAML Files Description

### 1. Namespace
- **`arafat-al-mahmud-namespace.yaml`**: Creates the namespace `arafat-al-mahmud-ns` to isolate all application resources.

### 2. Deployments

#### Frontend
- **`ostad-ui-deployment.yaml`**: Deploys the React frontend application
  - Image: `arafat89/ostad-ui:latest`
  - Port: 3000
  - Replicas: 2
  - Resource limits: 1Gi memory, 500m CPU
  - Resource requests: 512Mi memory, 250m CPU

#### Backend
- **`ostad-server-deployment.yaml`**: Deploys the Node.js backend API
  - Image: `arafat89/ostad-server:latest`
  - Port: 5050
  - Replicas: 2
  - Resource limits: 1Gi memory, 500m CPU
  - Resource requests: 512Mi memory, 250m CPU

#### Database
- **`mongo-deployment.yaml`**: Deploys MongoDB database
  - Image: `mongo:latest`
  - Port: 27017
  - Replicas: 1
  - Uses Persistent Volume Claim for data persistence
  - Environment variables from Kubernetes Secret
  - Resource limits: 1Gi memory, 500m CPU
  - Resource requests: 512Mi memory, 250m CPU

#### Admin Panel
- **`mongo-express-deployment.yaml`**: Deploys Mongo Express admin interface
  - Image: `mongo-express:latest`
  - Port: 8081
  - Replicas: 1
  - Configured to connect to MongoDB
  - Environment variables from Kubernetes Secret

### 3. Services

#### Frontend Service
- **`ostad-ui-service.yaml`**: Exposes the frontend application
  - Type: ClusterIP
  - Port: 80
  - Target Port: 3000

#### Backend Service
- **`ostad-server-service.yaml`**: Exposes the backend API
  - Type: ClusterIP
  - Port: 80
  - Target Port: 5050

#### Database Service
- **`mongo-service.yaml`**: Exposes MongoDB internally
  - Type: ClusterIP
  - Port: 27017
  - Target Port: 27017

#### Admin Panel Service
- **`mongo-express-service.yaml`**: Exposes Mongo Express admin panel
  - Type: ClusterIP
  - Port: 8081
  - Target Port: 8081

### 4. Ingress Resources

#### Frontend Ingress
- **`ostad-ui-ingress.yaml`**: Routes external traffic to the frontend
  - Host: `chat.local`
  - Path: `/`
  - Routes to ostad-ui service

#### Backend API Ingress
- **`ostad-api-ingress.yaml`**: Routes API traffic to the backend
  - Host: `api.chat.local`
  - Path: `/api`
  - Routes to ostad-server service

#### Admin Panel Ingress
- **`mongo-express-ingress.yaml`**: Routes admin traffic to Mongo Express
  - Host: `mongo.chat.local`
  - Path: `/`
  - Routes to mongo-express service

### 5. Storage

#### Persistent Volume Claim
- **`mongo-pvc.yaml`**: Claims persistent storage for MongoDB data
  - Access Mode: ReadWriteOnce
  - Storage Class: Default
  - Size: 1Gi

### 6. Cluster Configuration

#### Kind Cluster Config
- **`kind-config.yaml`**: Configuration for local Kind cluster setup
  - Includes ingress controller setup
  - Port mappings for external access

## Prerequisites

1. **Kubernetes Cluster**: Kind cluster with ingress controller
2. **kubectl**: Kubernetes command-line tool
3. **Local Host Configuration**: Add the following entries to `/etc/hosts` file:
   ```
   127.0.0.1 chat.local
   127.0.0.1 mongo.local
   ```

## Deployment Instructions

### 1. Create Namespace
```bash
kubectl apply -f arafat-al-mahmud-namespace.yaml
```

### 2. Create MongoDB Secret
```bash
kubectl create secret generic mongo-secret \
  --from-literal=username=admin \
  --from-literal=password=password123 \
  -n arafat-al-mahmud-ns
```

### 3. Deploy Storage
```bash
kubectl apply -f mongo-pvc.yaml
```

### 4. Deploy Database
```bash
kubectl apply -f mongo-deployment.yaml
kubectl apply -f mongo-service.yaml
```

### 5. Deploy Backend
```bash
kubectl apply -f ostad-server-deployment.yaml
kubectl apply -f ostad-server-service.yaml
```

### 6. Deploy Frontend
```bash
kubectl apply -f ostad-ui-deployment.yaml
kubectl apply -f ostad-ui-service.yaml
```

### 7. Deploy Admin Panel
```bash
kubectl apply -f mongo-express-deployment.yaml
kubectl apply -f mongo-express-service.yaml
```

### 8. Configure Ingress
```bash
kubectl apply -f ostad-ui-ingress.yaml
kubectl apply -f ostad-api-ingress.yaml
kubectl apply -f mongo-express-ingress.yaml
```

## Verification

### Check All Resources
```bash
kubectl get all -n arafat-al-mahmud-ns
```

### Check Pod Status
```bash
kubectl get pods -n arafat-al-mahmud-ns
```

### Check Services
```bash
kubectl get services -n arafat-al-mahmud-ns
```

### Check Ingress
```bash
kubectl get ingress -n arafat-al-mahmud-ns
```

## Access Points

After deployment, the application will be accessible at:

- **Frontend**: http://chat.local
- **Backend API**: http://chat.local/api
- **Mongo Express Admin**: http://mongo.local

## Features Implemented

✅ **Bonus Features Implementation**:
- CPU and memory resource requests and limits for all deployments
- Persistent Volume Claim for MongoDB storage
- Kubernetes Secrets for secure environment variable management
- Multiple replicas for high availability (frontend and backend)
- Proper ingress configuration with host-based routing
