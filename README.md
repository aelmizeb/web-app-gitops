# GitOps Demo Web App (Minikube + ArgoCD + Ingress)

This project is a simple static web page served through Kubernetes using:

- ✅ Minikube as the local cluster
- ✅ Argo CD for GitOps-based deployment
- ✅ NGINX Ingress Controller for routing HTTP traffic
- ✅ Docker for building the container image



## 📦 Project Structure (only required files)

```bash
web-app-gitops/
├── app/
│ ├── deployment.yaml # Web app deployment
│ ├── service.yaml # ClusterIP service exposing the app
│ └── ingress.yaml # Ingress resource (webapp.local)
│
├── argocd-app.yaml # argocd configs
├── index.html # Static HTML to serve
└── Dockerfile # Image that serves the HTML with Nginx
```

## 🚀 Prerequisites

- [Docker](https://www.docker.com/)
- [Minikube](https://minikube.sigs.k8s.io/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [ArgoCD CLI](https://argo-cd.readthedocs.io/en/stable/cli_installation/)


## 🐳 Build & Push Docker Image
⚠️ Replace **aelmizeb** with your actual Docker Hub username.

```bash
# Clone the repo and go inside
cd web-app-gitops

# Build your image
docker build -t aelmizeb/web-app:1.0.0 .

# Push to DockerHub
docker push aelmizeb/web-app:1.0.0
```

## 🧪 Minikube Setup
you can take a look to this to set up Minikube and ArgoCD on a Linux system : https://gist.github.com/aelmizeb/539f12687e551cef17b1fcfd14e19fb9

```bash
# Start Minikube with ingress support
minikube start
minikube addons enable ingress

# Check Ingress Controller is running
kubectl get pods -n ingress-nginx
```

## ⚙️ Deploy with ArgoCD
```bash
# Create a namespace for ArgoCD
kubectl create namespace argocd

# Install ArgoCD (non-HA for local)
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Port-forward the ArgoCD UI
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
Now open http://localhost:8080
Default credentials:
- username: admin
- password: run the following CLI to get it
  ```bash
  argocd admin initial-password -n argocd
  ```
Apply the configuration for ArgoCD
```bash
kubectl apply -f argocd-app.yaml -n argocd
```
## 🌐 Access the Web App
Add to /etc/hosts:

```bash
echo "$(minikube ip) webapp.local" | sudo tee -a /etc/hosts
```

Then open: http://webapp.local

## 🧹 Cleanup
```bash
argocd app delete web-app --cascade
```
This command deletes the ArgoCD managed application named web-app

```bash
minikube delete
```
This completely deletes the entire Minikube cluster:
 - Shuts down the Minikube virtual machine or container.
 - Removes all pods, services, Ingresses, volumes, namespaces, configurations, etc.
 - Essentially resets your local Kubernetes environment.

⚠️ This is irreversible: all local cluster data is lost.
minikube delete

## 📄 License

MIT License
