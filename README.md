# Kubernetes Web Application Deployment

This repository contains Kubernetes configuration files for deploying a web application with a frontend (NGINX), backend (Tomcat), and MySQL database. The application is exposed via an NGINX Ingress controller, routing traffic based on URL paths.

---

## 📘 Project Overview

The application consists of three main components:

- **Frontend:** An NGINX-based web server serving static content, accessible at the root path (`/`).
- **Backend:** A Tomcat-based application handling API requests, accessible at the `/student` path.
- **MySQL Database:** A persistent MySQL instance storing data for the backend, with configurations managed via `ConfigMap` and `Secret`.

Kubernetes resources ensure scalability, persistence, and secure communication between components.

---

## ⚙️ Prerequisites

To deploy this application, you need:

- A Kubernetes cluster (e.g., Minikube, GKE, EKS, or AKS)
- `kubectl` configured to interact with your cluster
- An NGINX Ingress controller installed in the cluster. 

Install it using:

  ```bash
  kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
  ```

- A domain name (e.g., `shivendra.rocks`) configured to point to your Ingress controller's external IP.
- `git` to clone this repository.
- Docker images `shiv9203/nginx:v2` and `shiv9203/my-tomcat:v2` available in a container registry.

---

## 📁 Repository Structure

The repository includes the following Kubernetes configuration files:

- **mysql-configmap.yaml:** Defines environment variables for the MySQL database (e.g., database name, user, host).
- **mysql-secret.yaml:** Stores sensitive data like MySQL root and user passwords (base64 encoded).
- **mysql-pv.yaml:** Configures a PersistentVolume for MySQL data storage.
- **mysql-pvc.yaml:** Requests storage via a PersistentVolumeClaim for MySQL.
- **mysql-service.yaml:** Exposes the MySQL database within the cluster on port 3306.
- **mysql-statefulset.yaml:** Deploys a single MySQL instance with persistent storage.
- **backend-deployment.yaml:** Deploys the Tomcat-based backend, connecting to MySQL.
- **backend-service.yaml:** Exposes the backend via a LoadBalancer service on port 8080.
- **frontend-deployment.yaml:** Deploys the NGINX-based frontend.
- **frontend-service.yaml:** Exposes the frontend within the cluster on port 80.
- **ingress.yaml:** Configures an NGINX Ingress to route traffic to the frontend (`/`) and backend (`/student`).

---

## 🚀 Setup Instructions

### 1. Clone the Repository:

```bash

git clone https://github.com/shivendra9203/Kubernetes-Web-Application-Deployment.git
cd <repository-directory>

```

### 2. Apply Kubernetes Configurations

Apply all YAML files to your Kubernetes cluster in the following order to ensure dependencies are met:

```bash

kubectl apply -f mysql-pv.yaml
kubectl apply -f mysql-pvc.yaml
kubectl apply -f mysql-configmap.yaml
kubectl apply -f mysql-secret.yaml
kubectl apply -f mysql-service.yaml
kubectl apply -f mysql-statefulset.yaml
kubectl apply -f backend-deployment.yaml
kubectl apply -f backend-service.yaml
kubectl apply -f frontend-deployment.yaml
kubectl apply -f frontend-service.yaml
kubectl apply -f ingress.yaml

```

### 3. Verify Deployments

```bash

kubectl get pods -n default
kubectl get services -n default
kubectl get ingress -n default

```

### 4. Configure DNS

Update your domain (`shivendra.rocks`) to point to the Ingress controller’s external IP. Retrieve the IP using:

```bash

kubectl get ingress app-ingress -n default

```

### 5. Access the Application:

- **Frontend:** Visit `http://shivendra.rocks/` in your browser.
- **Backend:** Access `http://shivendra.rocks/student` for API endpoints.

---

## 🛠 Usage

- The frontend serves static content or a web interface at the root URL.
- The backend handles requests at `/student`, interacting with the MySQL database.
- The MySQL database is accessible only within the cluster (via the `mysql` service) and is used by the backend.

---

## ❗ Troubleshooting

- **Pods Not Running:**
  - Check pod logs: `kubectl logs <pod-name> -n default`.
  - Ensure the Docker images (`shiv9203/nginx:v2`, `shiv9203/my-tomcat:v2`) are accessible.

- **Ingress Not Working:**
  - Verify the NGINX Ingress controller is running: `kubectl get pods -n ingress-nginx`.
  - Ensure your domain’s DNS is correctly configured.

- **MySQL Connection Issues:**
  - Confirm the ConfigMap and Secret values are correct.
  - Check the MySQL service: `kubectl describe service mysql -n default`.

---

## 📌 Notes

- The MySQL passwords in `mysql-secret.yaml` are base64 encoded. Decode them (`password` for root, `msa_pass` for the user) for debugging if needed.
- The backend and MySQL share the same PersistentVolumeClaim (`mysql-pvc`), which may cause conflicts. Consider creating a separate PVC for the backend if needed.
- The backend service is exposed as a `LoadBalancer`, which may require cloud provider support. For local testing (e.g., Minikube), consider changing it to `ClusterIP` or using `minikube tunnel`.

---

## 🤝 Contributing

Feel free to submit issues or pull requests to improve the configuration or add features. Ensure all changes are tested in a Kubernetes environment.

---

## 📄 License

This project is licensed under the `MIT License`. See the LICENSE file for details.
