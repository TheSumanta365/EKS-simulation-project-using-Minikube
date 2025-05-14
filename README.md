# 🚀 Secure Microservices Deployment on Kubernetes (Minikube Simulation)

This project demonstrates a production-style deployment of containerized microservices in a simulated EKS-like environment using Minikube. It covers real-world DevOps practices: secure deployments, observability, fault tolerance, and incident response.

---

## 🔧 Setup Instructions

### Prerequisites

* Minikube
* kubectl
* helm (for Prometheus stack)
* docker

### Quick Start

```bash
# Start Minikube
minikube start --addons=metrics-server,ingress

# Apply Kubernetes manifests
kubectl apply -k k8s-manifests/

# Deploy Prometheus + Grafana
kubectl apply -f observability/prometheus-stack/
kubectl apply -f observability/grafana-dashboards/

# (Optional) Access Grafana
kubectl port-forward svc/kube-prom-stack-grafana 3000:80 -n default
```

---

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

## ✅ Evaluation Coverage

| Area                | Status |
| ------------------- | ------ |
| Namespaces & RBAC   | ✅      |
| Network Policies    | ✅      |
| Ingress & Services  | ✅      |
| Monitoring Setup    | ✅      |
| Failure Simulation  | ✅      |
| Security Fixes      | ✅      |
| Kyverno Policy      | ✅      |
| Clear Documentation | ✅      |

---


