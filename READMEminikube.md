```markdown
# Running the MCA DevOps App on Minikube – Step-by-Step Guide

This guide explains how to build and deploy the Spring Boot backend, Angular frontend, and PostgreSQL database all on your local Minikube Kubernetes cluster using Helm and Docker.

---

## 1. Prerequisites

- [Minikube](https://minikube.sigs.k8s.io/docs/) installed
- [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl) installed
- [Helm](https://helm.sh/docs/intro/install/) installed
- Docker installed for building images

---

## 2. Start Minikube

Start Minikube with common add-ons:
```
minikube start --cpus=4 --memory=6g --addons=ingress,dashboard,metrics-server
```

---

## 3. Build Docker Images

### Backend

```
cd backend
docker build -t localhost:52156/mca-backend:latest .
```

### Frontend

```
cd ../frontend
docker build -t localhost:52156/mca-frontend:latest .
```

### Load Images into Minikube

```
minikube image load localhost:52156/mca-backend:latest
minikube image load localhost:52156/mca-frontend:latest
```
Or if using a local registry, make sure it's accessible to your Minikube nodes.

---

## 4. Deploy PostgreSQL Using Helm

```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install mca-postgresql bitnami/postgresql \
  --set auth.database=postgres \
  --set auth.username=myapplication \
  --set auth.password=M3P@ssw0rd!
```
- The service name will be `mca-postgresql-postgresql` in your namespace.

---

## 5. Configure the Application's Helm `values.yaml`

Edit your `mca-devops-app/values.yaml`:
```
backend:
  image:
    repository: localhost:52156/mca-backend
    tag: latest
  env:
    SPRING_POSTGRES_URL: "jdbc:postgresql://mca-postgresql-postgresql:5432/postgres"
    SPRING_POSTGRES_USERNAME: "myapplication"
    SPRING_POSTGRES_PASSWORD: "M3P@ssw0rd!"

frontend:
  image:
    repository: localhost:52156/mca-frontend
    tag: latest
```

---

## 6. Deploy the Application with Helm

```
cd ../mca-devops-app
helm upgrade --install mca . -f values.yaml
```

---

## 7. Configure Ingress

Make sure your `values.yaml` (or another value file) enables ingress:
```
ingress:
  enabled: true
  hosts:
    - host: mca-app.local
      paths:
        - path: /
          pathType: Prefix
          service: frontend
        - path: /api
          pathType: Prefix
          service: backend
```

Add this to your local `/etc/hosts`:
```
echo "$(minikube ip) mca-app.local" | sudo tee -a /etc/hosts
```
---

## 8. Access the Application

- Check resources:
  ```
  kubectl get pods
  kubectl get services
  kubectl get ingress
  helm status mca
  ```
- Visit [http://mca-app.local](http://mca-app.local) in your browser.

---

## 9. Troubleshooting

- Check backend/frontend logs:
  ```
  kubectl logs deployment/mca-mca-devops-app-backend
  kubectl logs deployment/mca-mca-devops-app-frontend
  ```
- Ensure all pods, especially PostgreSQL, are in `RUNNING` or `READY` state before accessing the app.

---

## Summary Table

| Step                    | Command / Action                                   |
|-------------------------|----------------------------------------------------|
| Start Minikube          | `minikube start ...`                               |
| Build Docker images     | `docker build ...`                                 |
| Load images             | `minikube image load ...`                          |
| Deploy PostgreSQL       | `helm install mca-postgresql ...`                  |
| Set Helm values         | Edit `values.yaml`                                 |
| Deploy app              | `helm upgrade --install mca ...`                   |
| Configure hosts         | Add `mca-app.local` to `/etc/hosts`                |
| Access app              | Open `http://mca-app.local`                        |

---

You now have a fully working local Kubernetes deployment of the MCA DevOps reference stack, operating entirely on your Minikube cluster!
```
