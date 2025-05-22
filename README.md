# 🛠️ Helm-Based Kubernetes Deployment (Local) - Pools App

This branch demonstrates deploying the Pools App (Frontend + Backend + MongoDB) locally using Helm, the package manager for Kubernetes.


## 📦 What is Helm?

Helm is to Kubernetes what:

- `apt` is to Debian-based Linux

- `yum` is to CentOS/RHEL

It helps you define, install, upgrade, and manage Kubernetes applications using a packaging format called charts.


---

## 📁 Project Structure
```graphql
kubernetes-deployment-local/
│
├── Pools-App-Backend/               # Source code for the backend service (Node.js)
├── Pools-App-Frontend/              # Source code for the frontend application (e.g., Angular/React)
│
├── backend-deployment.yaml          
├── frontend-deployment.yaml         
├── mongodb-deployment.yaml          
│
├── mongo-configmap.yaml             
├── mongodb-secrets.yaml             
├── poolsapp/                        # HELM CHART 
│       ├── Chart.yaml
│       ├── values.yaml
│       ├── charts/
│       └── templates/
│           ├── deployment.yaml (merged from all original templates)
│           ├── service.yaml
│           └── (other customized templates)
├── LICENSE                          
├── poolsapp-README.md               # Documentation specific to the Pools App
├── poolsapp-LICENSE                 # License specific to the Pools App (if separate from main LICENSE)
└── README.md                        # The file you are reading now.                                    
```

---

## 🔧 Prerequisites

- Docker Desktop with **Kubernetes enabled**
- `kubectl` CLI installed

---

## ⚙️ How to Use Helm for Deployment

### Step 1: Let's Create a Helm Chart
Use the follwoing command to do so
```bash
helm create <name-of-Chart>

helm create poolsapp                <-- Eg.
```
This will generate the following default chart structure:

- Chart.yaml    – Chart metadata

- values.yaml   – Default configuration values

- templates/    – Contains YAML templates like deployment.yaml, service.yaml, ingress.yaml, etc.

- .helmignore   – Files to ignore during packaging

### Step 2: Move Kubernetes Templates into Chart

- Copy your existing Kubernetes YAMLs into poolsapp/templates/

- Convert static values to use Helm’s templating syntax ({{ .Values.<key> }})

####  Example:

Template – deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Values.backend.deploymentName }}
spec:
  replicas: {{ .Values.backend.replicas }}
  selector:
    matchLabels:
      app: {{ .Values.backend.podName }}
```
#### Referenced in – values.yaml
```yaml
backend:
  deploymentName: pools-app-backend-deployment
  replicas: 1
  podName: pools-app-backend-pod
```

### Step 3: Lint the Chart
```bash
helm lint ./poolsapp
```
✅ This checks for syntax issues and chart formatting problems.


### Step 4: Package the Chart
```bash
helm package ./poolsapp
```
This generates a .tgz archive,  
e.g. poolsapp-0.1.0.tgz, which can be shared or installed.


### Step 5: Install the Helm Chart

```bash
helm install my-poolsapp ./poolsapp-0.1.0.tgz
```
### Step 6: Upgrade (if needed)

```bash
helm upgrade my-poolsapp ./poolsapp
```

### Step 7: Uninstall (Cleanup)

```bash
helm uninstall my-poolsapp
```

---

### 🔎 Useful Helm Commands

| Task                                    | Command                                        |
| --------------------------------------- | ---------------------------------------------- |
| List Helm releases in current namespace | `helm list`                                    |
| List all releases across namespaces     | `helm list --all-namespaces` or `helm list -A` |
| View release status                     | `helm status <release>`                        |
| View all manifest details of a release  | `helm get all <release>`                       |
| Print Helm environment variables        | `helm env`                                     |
---

### 🧠 Important:
Run helm lint before packaging. helm package will still work if the chart is invalid, but may result in a broken .tgz.

---

### 🌐 Official Helm Docs

For more examples and a CLI reference, visit:
👉 https://helm.sh/docs/intro/cheatsheet


## ✅  Verifying Deployment & Debugging

After you deploy your app using Helm, it’s important to verify that everything is running as expected. Below are useful tips and commands.

### 🔍 Check Pods and Services
#### List Pods with IPs and Node Details:
---
```bash
kubectl get pods -o wide
```
#### List Services and Their Cluster IPs:
---
```bash
kubectl get svc
```
#### Check Helm Release Status:
---
```bash
helm status <release-name>
```
    This will show:

    -   Release namespace

    -   Deployment status

    -   Resources created

    -   Notes from templates/notes.txt (if defined)

### 📄 View Pod Logs
#### Single Pod Logs:
---
```bash
kubectl logs <pod-name>
```

#### 💡 Use -f to follow logs:
---
```bash
kubectl logs <pod-name> -f
```

---

## 🧪 Debug Tools

If a container is crashing or DNS fails, try these:
#### Run a debug pod with networking tools:
---
```bash
kubectl run -it debug --rm --image=busybox --restart=Never -- sh
```
Inside it, test DNS resolution:
```sh
nslookup pools-app-frontend-service
```

#### Describe Failing Pod:
---
```bash
kubectl describe pod <pod-name>
```
You’ll get:

- Container state

- Events

- Mount paths

- Probes

- Volume issues (if any)

## 📌 Tip: Check All Resources Created by Helm

```bash
kubectl get all -l app=<your-label>
```

Or list everything in the namespace:
```bash
kubectl get all -n <namespace>
```

## 🔮 What’s Next?

This branch currently demonstrates a basic Helm deployment (no ingress, HPA, or service accounts yet). In upcoming commits, we will:

    Add Ingress configuration

    Add HorizontalPodAutoscaler (HPA)

    Add advanced values.yaml structures

    Enhance the chart with reusable templates and helpers

Stay tuned!!