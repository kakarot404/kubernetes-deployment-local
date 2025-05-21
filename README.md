# 🐳 Kubernetes Deployment of Full-Stack App (Frontend + Backend + MongoDB)

This project demonstrates how to deploy a complete open-source application consisting of a **frontend**, **backend**, and **MongoDB database** using **Kubernetes** on a **local machine**. This setup is built and run using Docker Desktop, but can also be run with Minikube. It's designed to showcase my DevOps skills including containerization, Kubernetes configuration, secret management, and service orchestration.

---

## 📁 Project Structure
```graphql
kubernetes-deployment-local/
│
├── Pools-App-Backend/               # Source code for the backend service (Node.js)
├── Pools-App-Frontend/              # Source code for the frontend application (e.g., Angular/React)
│
├── backend-deployment.yaml          # Kubernetes Deployment and Service definition for the backend
├── frontend-deployment.yaml         # Kubernetes Deployment and Service definition for the frontend
├── mongodb-deployment.yaml          # Kubernetes Deployment and Service definition for MongoDB
│
├── mongo-configmap.yaml             # Kubernetes ConfigMap for MongoDB connection URI
├── mongodb-secrets.yaml             # Kubernetes Secret for storing MongoDB credentials
│
├── LICENSE                          # License for the overall project
├── poolsapp-README.md               # Documentation specific to the Pools App
├── poolsapp-LICENSE                 # License specific to the Pools App (if separate from main LICENSE)
└── README.md                        # Main documentation for the DevOps Kubernetes deployment project                                     
```

---

## 🚀 Technologies Used

- **Kubernetes**    (local cluster via Docker Desktop)
- **Docker**        (containerization)
- **ConfigMaps** and **Secrets** (secure configuration management)
- **Services**      (`LoadBalancer` and `ClusterIP`)
- **MongoDB**       (NoSQL database)
- **Node.js Backend** and **Frontend App** (e.g., Angular or React)

---

## 🔧 Prerequisites

- Docker Desktop with **Kubernetes enabled**
- `kubectl` CLI installed

---

## ⚙️ Deployment Steps

### Step 1: Lets start Kubernetes Cluster
Ensure Kubernetes is running via Docker Desktop (Or Minikube).
( Docker Desktop Settings -> Kubernetes -> Enable Kubernetes)

Verify with:
```bash
kubectl cluster-info            OR
kubectl version --client
```

### Step 2: Create MongoDB Secrets and MongoDB ConfigMap
```bash
kubectl apply -f mongodb-secrets.yaml
kubectl apply -f mongo-configmap.yaml
```

### Step 3: Deploy MongoDB
```bash
kubectl apply -f mongodb-deployment.yaml
```
Wait until the pod is running and check:
```bash
kubectl get pods
```

### Step 4: Deploy Backend and Frontend
```bash
kubectl apply -f backend-deployment.yaml
kubectl apply -f frontend-deployment.yaml
```

### Step 5: Access the App Now

Check service ports:
```bash
kubectl get services
```
Eg. 
```ruby
NAME                                 TYPE               PORT(S)
service/kubernetes                   ClusterIP          443/TCP
service/pools-app-backend-service    LoadBalancer       1234:32149/TCP
service/pools-app-frontend-service   LoadBalancer       4200:31134/TCP
service/pools-app-mongodb-service    ClusterIP          27017/TCP
```

### 🌐 Access the App:

    Frontend: http://localhost:4200

Note: Docker Desktop maps service ports directly to localhost.


## ✅ Verifying Deployment

Run ```bash kubectl get all``` to see pods, deployments, and services.

Check logs:

```bash
kubectl logs <pod-name>
```
You can test backend API endpoints via Postman or curl.

And use browser to interact with the frontend.


## 📝 Notes and Tips

- Use the correct image & tag in your deployment YAML files.

- 🐳 If you modify code/templates, make sure to push the updated image to Docker Hub or your local registry.

- For troubleshooting Pods:
```bash
kubectl get pods
kubectl describe pod <pod-name>
```

- 🔁 If you change the name of your backend service, don't forget to update the frontend proxy config accordingly.


## 🔧 Networking & DNS Utilities

Kubernetes networking is abstract. Use the following commands to debug:

| Task                       | Command                                |
| -------------------------- | -------------------------------------- |
| See Pod IPs                | `kubectl get pods -o wide`             |
| See Services and their IPs | `kubectl get svc -o wide`              |
| View Network Policies      | `kubectl get networkpolicy`            |
| DNS resolution             | `kubectl exec <pod> -- nslookup <svc>` |
| Check CNI plugin           | `kubectl get pods -n kube-system`      |
| Check Service Endpoints    | `kubectl get endpoints`                |

## 🧪 Debugging with a Utility Pod

Because slim containers often lack DNS tools (nslookup, dig, etc.), use a debug pod like this:
```bash
kubectl run -it debug --rm --image=busybox --restart=Never -- sh
```
Inside the shell:
```bash
nslookup pools-app-frontend-service
```

    Example output:

    Server:         10.96.0.10            ← Cluster DNS Server IP (CoreDNS)
    Address:        10.96.0.10:53

    Name:   pools-app-frontend-service.default.svc.cluster.local
    Address: 10.106.81.20                ← ClusterIP for the frontend service

## 🧠 This confirms:

- The service exists

- It's in the default namespace

- DNS resolution is working as expected

## 🧹 Cleanup or Pause the App
To delete deployments and services:
```bash
kubectl delete deployment <deployment-1> <deployment-2> ...
kubectl delete service <service-1> <service-2> ...
```
To pause (stop without deleting):
```bash
kubectl scale deployment <deployment-name> --replicas=0
```

## 🚀 DevOps Highlights

- Declarative infrastructure with Kubernetes YAML files

- Secure credential handling via Secrets

- Environment configuration using ConfigMaps

- Modular deployment for database, backend, and frontend

- Local Kubernetes environment simulating production

## 📦 What’s Next?

This is the manual Kubernetes setup. In the next branch, we'll migrate this deployment to use Helm for better reusability, templating, and configurability.

Stay tuned!