# 🚀 Secure Microservices Deployment on Kubernetes (Minikube Simulation)

This project demonstrates a production-style deployment of containerized microservices in a simulated EKS-like environment using Minikube. It covers real-world DevOps practices: secure deployments, observability, fault tolerance, and incident response.

---

## 🔧 Setup Instructions

### Prerequisites

* Minikube
* kubectl
* helm
* docker
* prometheus and grafana(setup by helm)

### Quick Start

```bash
# Start Minikube
minikube start --addons=metrics-server,ingress

# Apply Kubernetes manifests
kubectl apply -k manifests/app-services/auth-service

kubectl apply -k manifests/app-services/data-service

kubectl apply -k manifests/app-services/gateway

# Deploy Prometheus + Grafana
kubectl apply -f observability/prometheus-stack/
kubectl apply -f observability/grafana-dashboards/

# Access Grafana UI
kubectl port-forward svc/kube-prom-stack-grafana 3000:80 -n default

# Fetch the ip and put in hosts file
minikube ip
sudo nano /etc/hosts
<minikube-ip>   gateway.local #add in the last line
# Activate minikube tunnel
 minikube tunnel
# Access the Application
#go to the browser and access the entry point by typing
gateway.local #shows the nginx hello page
gateway.local/auth #shows the httpbin.org page
gateway.local/data #shows "hello from data service"
```
---
## 🧭 Architecture Diagram

                                 +---------------------------+
                                 |       External User       |
                                 +------------+--------------+
                                              |
                                              v
                                 +------------+--------------+
                                 |       Ingress (NGINX)     |  <- gateway.local
                                 |    (gateway-ingress)      |
                                 +------+------+-------------+
                                        |      |
                     ------------------+      +-------------------
                    |                                        |
                    v                                        v
         +---------------------+                +----------------------+
         |   gateway service   |                |  /data -> data-service |
         | ClusterIP:80        |                | ClusterIP:5678        |
         +----------+----------+                +----------+-----------+
                    |                                     |
                    v                                     v
         +------------------------+         +---------------------------+
         |  gateway pod (80)      |         |   data-service pod (5678) |
         +------------------------+         +---------------------------+

                                  |
                                  +-----------------------------------+
                                  |                                   |
                                  v                                   v
                 +-----------------------------+      +-----------------------------+
                 | /auth -> auth-service       |      |  Minio (internal)           |
                 | ClusterIP:80                |      | Access tested by test pods  |
                 +-------------+---------------+      +-----------------------------+
                               |
                               v
              +-------------------------------+
              |   auth-service pod (80)       |
              +-------------------------------+

Notes:
- All services run in the `app` namespace.
- Ingress is handled by NGINX, routing `/`, `/auth`, and `/data` based on the path.
- NetworkPolicies isolate services, restricting unauthorized cross-communication.
- Minio is tested internally with job/test pods.


## 🛡️ Key Features

### 🔐 Security

* RBAC and scoped service accounts
* NetworkPolicies for service isolation
* Secrets managed via Kubernetes
* Kyverno policy to block sensitive env vars
* Authorization header leak fixed in `auth-service`

### 📈 Observability

* Prometheus + Grafana dashboards
* Resource usage tracking
* Alerting rules (high CPU, pod restarts, etc.)

### 🔄 Failure Handling

* Pod failure recovery via ReplicaSets
* Simulated node pressure tested
* Observed system health via Grafana

---

## 🧪 Security Incident Simulation

**Incident:** `auth-service` leaked `Authorization` headers
**Fix:**

* Disabled logging of sensitive headers
* Applied egress NetworkPolicy to limit external communication
* Added Kyverno rule to block pods with exposed sensitive envs

---

## 🧠 Design Decisions

* Separate namespaces for apps, system tools, and monitoring
* MinIO used as local S3-compatible storage
* Ingress controller used to mimic AWS ALB
* Manual failure testing to simulate operational challenges

---

## 📝 Deliverables

* `README.md`: Setup, decisions, and incident response
* `k8s-manifests/`: Deployments, Services, NetworkPolicies, Secrets
* `observability/`: Prometheus stack and exported Grafana dashboards
* `policies/`: Kyverno policy files
* Dockerfiles (if any custom changes made)

---

## ⚠️ Known Issues

* `minikube tunnel` must be running for ingress access
* Policies are applied in audit mode for demo purposes
* Not integrated with full CI/CD in this setup

---

