# Simple MongoDB & Mongo Express Kubernetes Deployment

This project contains Kubernetes manifest files to deploy a **MongoDB** database and **Mongo Express** (a web-based MongoDB admin interface) using **StatefulSets** and supporting configurations.

---
## 📋 Prerequisites
 Before you begin, ensure you have the following installed and configured:

#### Kubernetes Cluster
You can use Minikube, kind, or a managed Kubernetes service (e.g., GKE, EKS, AKS).

#### kubectl CLI
Install from: https://kubernetes.io/docs/tasks/tools/

#### Namespace (optional)
You can create a separate namespace for this deployment:
```bash
kubectl create namespace mongo
```
#### Docker (optional)
Only needed if you plan to build custom images.

## 📁 Project Structure

| File Name | Description |
|------------|--------------|
| `configmap.yaml` | Defines environment variables and configuration data for MongoDB and Mongo Express. |
| `secrete.yaml` | Stores sensitive credentials (like MongoDB username and password) securely as Kubernetes Secrets. |
| `mongo.yaml` | Contains deployment and service configuration for MongoDB (if not using StatefulSet). |
| `mogo-statefulset.yaml` | Defines a StatefulSet for MongoDB to ensure stable network identity and persistent storage. |
| `mongo-express.yaml` | Contains deployment and service configuration for Mongo Express (if not using StatefulSet). |
| `mongo-express-statefulset.yaml` | Defines a StatefulSet for Mongo Express for persistent and stable deployment. |

---

## 🚀 Deployment Steps

### 1. Clone the Repository
```bash
git clone <your-repo-url>
cd <your-folder-name>
```
### 2. Apply the Kubernetes Manifests
```
Apply all YAML files in the folder to your Kubernetes cluster:
```bash
kubectl apply -f .
```
### 3. Verify Deployments

Check that all pods, services, and StatefulSets are running:
```bash
kubectl get pods
kubectl get svc
kubectl get statefulsets
```
### 🌐 Accessing Mongo Express

Once the deployment is complete, expose Mongo Express using a service or port-forwarding:
```bash
kubectl port-forward svc/mongo-express-service 8081:8081
```

Then open your browser and visit:

http://localhost:8081


Login using the credentials stored in your secrete.yaml file.

### 🧹 Cleanup

To remove all deployed resources:
```bash
kubectl delete -f .
```





