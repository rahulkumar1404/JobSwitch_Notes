# ☸️ Kubernetes — Complete Notes (Zero to Advance)
### Hindi Explanation + English Interview Answers

> 🏠 [Back to Index](./README.md) | 🐳 [Docker Notes](./Docker-Complete-Notes.md)

---

## Table of Contents
- [Chapter 1: Kubernetes Overview](#chapter-1-kubernetes-overview)
- [Chapter 2: Kubernetes Architecture](#chapter-2-kubernetes-architecture)
- [Chapter 3: Kubernetes Installation](#chapter-3-kubernetes-installation)
- [Chapter 4: kubectl — Complete Reference](#chapter-4-kubectl--complete-reference)
- [Chapter 5: Pods — Complete](#chapter-5-pods--complete)
- [Chapter 6: Init Containers & Sidecar Pattern](#chapter-6-init-containers--sidecar-pattern)
- [Chapter 7: Labels, Selectors & Annotations](#chapter-7-labels-selectors--annotations)
- [Chapter 8: Namespaces](#chapter-8-namespaces)
- [Chapter 9: ReplicaSets](#chapter-9-replicasets)
- [Chapter 10: Deployments](#chapter-10-deployments)
- [Chapter 11: Services](#chapter-11-services)
- [Chapter 12: Ingress & IngressClass](#chapter-12-ingress--ingressclass)
- [Chapter 13: ConfigMaps & Secrets](#chapter-13-configmaps--secrets)
- [Chapter 14: Taints & Tolerations](#chapter-14-taints--tolerations)
- [Chapter 15: Node Affinity & Pod Affinity](#chapter-15-node-affinity--pod-affinity)
- [Chapter 16: Resource Requests & Limits](#chapter-16-resource-requests--limits)
- [Chapter 17: ResourceQuota & LimitRange](#chapter-17-resourcequota--limitrange)
- [Chapter 18: Persistent Volumes](#chapter-18-persistent-volumes)
- [Chapter 19: StatefulSets](#chapter-19-statefulsets)
- [Chapter 20: DaemonSets](#chapter-20-daemonsets)
- [Chapter 21: Jobs & CronJobs](#chapter-21-jobs--cronjobs)
- [Chapter 22: RBAC](#chapter-22-rbac)
- [Chapter 23: Service Accounts](#chapter-23-service-accounts)
- [Chapter 24: Network Policies](#chapter-24-network-policies)
- [Chapter 25: Horizontal Pod Autoscaler (HPA)](#chapter-25-horizontal-pod-autoscaler-hpa)
- [Chapter 26: Vertical Pod Autoscaler (VPA)](#chapter-26-vertical-pod-autoscaler-vpa)
- [Chapter 27: Cluster Autoscaler](#chapter-27-cluster-autoscaler)
- [Chapter 28: Pod Disruption Budget (PDB)](#chapter-28-pod-disruption-budget-pdb)
- [Chapter 29: Helm — Package Manager](#chapter-29-helm--package-manager)
- [Chapter 30: DNS & Service Discovery (CoreDNS)](#chapter-30-dns--service-discovery-coredns)
- [Chapter 31: Monitoring — Prometheus & Grafana](#chapter-31-monitoring--prometheus--grafana)
- [Chapter 32: Logging — EFK Stack](#chapter-32-logging--efk-stack)
- [Chapter 33: Kubernetes Dashboard](#chapter-33-kubernetes-dashboard)
- [Chapter 34: Cluster Upgrades & Maintenance](#chapter-34-cluster-upgrades--maintenance)
- [Chapter 35: etcd Backup & Restore](#chapter-35-etcd-backup--restore)
- [Chapter 36: CRDs & Operators](#chapter-36-crds--operators)
- [Chapter 37: Pod Security (PSA)](#chapter-37-pod-security-psa)
- [Chapter 38: Admission Controllers](#chapter-38-admission-controllers)
- [Chapter 39: Production Checklist](#chapter-39-production-checklist)
- [Chapter 40: Kubernetes Interview Q&A](#chapter-40-kubernetes-interview-qa)
- [Chapter 41: Quick Reference Cheat Sheet](#chapter-41-quick-reference-cheat-sheet)

---

## Chapter 1: Kubernetes Overview

> 🔗 Related: [Architecture](#chapter-2-kubernetes-architecture) | [Installation](#chapter-3-kubernetes-installation)

### 🇮🇳 Hindi Explanation
100+ containers manually manage karna impossible hai — restart karna, scale karna, updates karna. **Kubernetes (K8s)** ye sab automatically karta hai.

**K8s kya karta hai:**
- ✅ **Self-healing** — Container crash hue? Auto-restart
- ✅ **Auto-scaling** — Load badha? Pods badha do
- ✅ **Rolling updates** — Zero downtime deploy
- ✅ **Service discovery** — Containers ek dusre ko dhundh sakte hain
- ✅ **Load balancing** — Traffic distribute karo
- ✅ **Secret management** — Passwords safely store karo
- ✅ **Storage orchestration** — Automatically storage attach karo

**Real life analogy:** Kubernetes ek **smart factory manager** hai — workers (containers) ko kaam deta hai, naya worker hire karta hai jab purana thak jaaye, aur production line smooth chalata hai.

**Why K8s (not just Docker Compose)?**
| Feature | Docker Compose | Kubernetes |
|---------|---------------|------------|
| Multi-host | ❌ Single host | ✅ Multi-host cluster |
| Auto-healing | ❌ Manual | ✅ Automatic |
| Auto-scaling | ❌ Manual | ✅ HPA/VPA |
| Rolling updates | ❌ Limited | ✅ Built-in |
| Production use | Dev/small | Enterprise standard |

### 🎯 Interview Answer
**Q: What is Kubernetes and why is it needed?**

Kubernetes (K8s) is an open-source container orchestration platform originally designed by Google, now maintained by CNCF. It automates deployment, scaling, and management of containerized applications across a cluster of machines. We need K8s because: Docker alone cannot manage containers across multiple hosts, doesn't auto-restart failed containers, lacks built-in load balancing and service discovery at scale, and has no built-in secret management. K8s solves all these with self-healing, horizontal pod autoscaling, rolling deployments, service mesh integration, and declarative configuration.

---

## Chapter 2: Kubernetes Architecture

> 🔗 Related: [kubectl](#chapter-4-kubectl--complete-reference) | [Pods](#chapter-5-pods--complete)

### 🇮🇳 Hindi Explanation
K8s cluster mein **2 types ke nodes** hote hain:
1. **Control Plane (Master)** — Brain — cluster manage karta hai
2. **Worker Nodes** — Hands — actual application run karte hain

```
┌──────────────────────────────────────────────────────────┐
│                    CONTROL PLANE                          │
│                                                           │
│  ┌──────────────┐  ┌─────────┐  ┌────────────────────┐  │
│  │ kube-         │  │  etcd   │  │  kube-scheduler    │  │
│  │ apiserver     │  │ (State  │  │  (Pod placement    │  │
│  │ (Front door)  │  │  Store) │  │   decisions)       │  │
│  └──────────────┘  └─────────┘  └────────────────────┘  │
│                                                           │
│  ┌──────────────────────────────────────────────────┐    │
│  │         kube-controller-manager                   │    │
│  │  Node Controller | Deployment | ReplicaSet | ...  │    │
│  └──────────────────────────────────────────────────┘    │
│                                                           │
│  ┌──────────────────────────────────────────────────┐    │
│  │         cloud-controller-manager (optional)       │    │
│  │  (AWS/GCP/Azure specific controllers)             │    │
│  └──────────────────────────────────────────────────┘    │
└──────────────────────────┬───────────────────────────────┘
                           │ (kube-apiserver communicates)
         ┌─────────────────┼──────────────────┐
         │                 │                  │
┌────────▼──────┐  ┌───────▼───────┐  ┌──────▼────────┐
│  Worker Node 1│  │ Worker Node 2 │  │ Worker Node 3 │
│               │  │               │  │               │
│  ┌─────────┐  │  │  ┌─────────┐  │  │  ┌─────────┐  │
│  │ kubelet │  │  │  │ kubelet │  │  │  │ kubelet │  │
│  └─────────┘  │  │  └─────────┘  │  │  └─────────┘  │
│  ┌──────────┐ │  │  ┌──────────┐ │  │  ┌──────────┐ │
│  │kube-proxy│ │  │  │kube-proxy│ │  │  │kube-proxy│ │
│  └──────────┘ │  │  └──────────┘ │  │  └──────────┘ │
│  ┌──────────┐ │  │  ┌──────────┐ │  │  ┌──────────┐ │
│  │containerd│ │  │  │containerd│ │  │  │containerd│ │
│  └──────────┘ │  │  └──────────┘ │  │  └──────────┘ │
│  [Pod][Pod]   │  │  [Pod][Pod]   │  │  [Pod]        │
└───────────────┘  └───────────────┘  └───────────────┘
```

### Control Plane Components

| Component | Kya karta hai | Analogy |
|-----------|--------------|---------|
| **kube-apiserver** | Cluster ka front door — sab requests yahan aati hain, REST API expose karta hai | Reception desk |
| **etcd** | Distributed key-value store — poore cluster ka state yahan save hota hai | Company database |
| **kube-scheduler** | Naya pod kaunse node pe chalega decide karta hai (resources, affinity, taints dekh ke) | HR Manager |
| **kube-controller-manager** | Multiple controllers run karta hai — desired state maintain karta hai | Factory supervisor |
| **cloud-controller-manager** | Cloud provider (AWS/GCP/Azure) ke saath integrate karta hai | Cloud liaison |

### Worker Node Components

| Component | Kya karta hai |
|-----------|--------------|
| **kubelet** | Node pe pod lifecycle manage karta hai — control plane ka agent |
| **kube-proxy** | Network rules (iptables/ipvs) maintain karta hai — Services ka routing |
| **Container Runtime** | Containers chalata hai — containerd (default), CRI-O, Docker (deprecated) |

### 🎯 Interview Answer
**Q: Explain Kubernetes Architecture in detail.**

K8s cluster has **Control Plane** and **Worker Nodes**. Control Plane: **kube-apiserver** — validates/processes all REST requests, only component that talks to etcd; **etcd** — distributed KV store persisting complete cluster state, uses Raft consensus; **kube-scheduler** — assigns pods to nodes based on resource requirements, affinity rules, taints/tolerations; **kube-controller-manager** — runs Node Controller (handles node failures), Deployment Controller, ReplicaSet Controller, Endpoints Controller etc.; **cloud-controller-manager** — manages cloud-specific resources (LoadBalancers, Volumes). Worker Nodes: **kubelet** — node agent that ensures pod specs are running; **kube-proxy** — implements Service networking via iptables/ipvs rules; **Container Runtime** — actually runs containers (containerd). Core principle: **desired state reconciliation** — controllers continuously watch and fix divergence between desired and actual state.

---

## Chapter 3: Kubernetes Installation

> 🔗 Related: [kubectl](#chapter-4-kubectl--complete-reference) | [Architecture](#chapter-2-kubernetes-architecture)

### 🇮🇳 Hindi Explanation
**3 main ways:**
1. **Minikube** — Local development (single node cluster)
2. **Kind (K8s in Docker)** — CI/CD, testing (Docker containers as nodes)
3. **kubeadm** — Production-grade cluster

### Minikube (Local Dev)
```bash
# Install (Linux)
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Start
minikube start                              # Default
minikube start --driver=docker              # Docker backend
minikube start --cpus=4 --memory=8192       # Resources
minikube start --kubernetes-version=v1.29.0 # Specific version
minikube start --nodes=3                    # Multi-node

# Manage
minikube status
minikube stop
minikube delete
minikube delete --all

# Features
minikube dashboard                          # Web UI
minikube tunnel                             # LoadBalancer services expose karo
minikube service myservice                  # Service URL
minikube addons list
minikube addons enable ingress
minikube addons enable metrics-server
minikube addons enable dashboard

# Docker env (Minikube ka Docker use karo)
eval $(minikube docker-env)
eval $(minikube docker-env --unset)         # Undo

# SSH into minikube
minikube ssh
```

### Kind (K8s in Docker)
```bash
# Install
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
chmod +x kind && sudo mv kind /usr/local/bin/

# Single node cluster
kind create cluster
kind create cluster --name mycluster

# Multi-node cluster
cat > kind-config.yaml << EOF
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 30080
    hostPort: 80
  - containerPort: 30443
    hostPort: 443
- role: worker
- role: worker
- role: worker
EOF
kind create cluster --config kind-config.yaml --name mycluster

# Manage
kind get clusters
kind delete cluster --name mycluster

# Image load karo (local image cluster mein)
kind load docker-image myapp:latest --name mycluster
```

### kubeadm (Production)
```bash
# ============================================
# ALL NODES PE (Master + Workers)
# ============================================
# Swap disable karo
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

# Container runtime (containerd) install karo
sudo apt-get update
sudo apt-get install -y containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo systemctl restart containerd && sudo systemctl enable containerd

# kubeadm, kubelet, kubectl install karo
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | \
  sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
  https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | \
  sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet=1.29.0-1.1 kubeadm=1.29.0-1.1 kubectl=1.29.0-1.1
sudo apt-mark hold kubelet kubeadm kubectl

# ============================================
# MASTER NODE PE ONLY
# ============================================
sudo kubeadm init \
  --pod-network-cidr=10.244.0.0/16 \
  --apiserver-advertise-address=<MASTER_IP>

# kubectl setup
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# CNI Plugin install karo (Flannel/Calico/Weave)
# Flannel:
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
# Calico:
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.0/manifests/calico.yaml

# ============================================
# WORKER NODES PE
# ============================================
# kubeadm init ke output se join command copy karo:
sudo kubeadm join <MASTER_IP>:6443 \
  --token <TOKEN> \
  --discovery-token-ca-cert-hash sha256:<HASH>

# Token expire ho gaya? New token:
kubeadm token create --print-join-command
```

### kubectl Install
```bash
# Linux
curl -LO "https://dl.k8s.io/release/$(curl -L -s \
  https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Verify
kubectl version --client

# Shell completion
echo 'source <(kubectl completion bash)' >> ~/.bashrc
echo 'alias k=kubectl' >> ~/.bashrc
echo 'complete -o default -F __start_kubectl k' >> ~/.bashrc
source ~/.bashrc
```

---

## Chapter 4: kubectl — Complete Reference

> 🔗 Related: [Pods](#chapter-5-pods--complete) | [Deployments](#chapter-10-deployments)

### 🇮🇳 Hindi Explanation
**kubectl** K8s ka command-line tool hai. Ye kube-apiserver se baat karta hai.

```bash
# ============================================
# CONFIG & CONTEXT
# ============================================
kubectl config view                         # Config dekho
kubectl config view --minify               # Current context only
kubectl config get-contexts                 # All contexts
kubectl config current-context
kubectl config use-context prod-cluster
kubectl config set-context --current --namespace=production
kubectl config delete-context old-cluster

# Kubeconfig file
export KUBECONFIG=~/.kube/config:~/.kube/prod-config  # Multiple configs
kubectl config view --flatten > ~/.kube/merged-config  # Merge karo

# ============================================
# GET — Resources list karo
# ============================================
kubectl get pods                            # Default namespace
kubectl get pods -n kube-system            # Specific namespace
kubectl get pods -A                         # All namespaces
kubectl get pods -o wide                    # Node + IP info bhi
kubectl get pods -o yaml                    # YAML format
kubectl get pods -o json                    # JSON format
kubectl get pods -o jsonpath='{.items[*].metadata.name}'
kubectl get pods -o custom-columns=\
  NAME:.metadata.name,STATUS:.status.phase,NODE:.spec.nodeName
kubectl get pods -w                         # Watch mode (live)
kubectl get pods --watch-only              # Sirf changes
kubectl get pods -l app=myapp              # Label selector
kubectl get pods -l app=myapp,env=prod
kubectl get pods -l 'env in (prod,staging)'
kubectl get pods --field-selector=status.phase=Running
kubectl get pods --field-selector=spec.nodeName=worker-1
kubectl get pods --sort-by=.metadata.creationTimestamp
kubectl get pods --sort-by=.status.startTime

# Multiple resources ek saath
kubectl get pods,services,deployments
kubectl get all                             # Common resources
kubectl get all -n myapp

# ============================================
# DESCRIBE — Detailed info
# ============================================
kubectl describe pod mypod
kubectl describe pod mypod -n production
kubectl describe node worker-1
kubectl describe deployment myapp
kubectl describe service myservice
kubectl describe ingress myingress
kubectl describe pvc mypvc
kubectl describe hpa myapp-hpa
# Events section mein problems dikhti hain!

# ============================================
# CREATE & APPLY
# ============================================
# Imperative (quick, not recommended for production)
kubectl run mypod --image=nginx
kubectl run mypod --image=nginx --port=80 --env="ENV=prod"
kubectl run mypod --image=nginx --dry-run=client -o yaml  # YAML generate
kubectl create deployment myapp --image=myapp:1.0
kubectl create deployment myapp --image=myapp:1.0 --replicas=3
kubectl create service clusterip myapp --tcp=80:3000
kubectl create configmap myconfig --from-literal=key=value
kubectl create secret generic mysecret --from-literal=pass=secret
kubectl create namespace mynamespace

# Declarative (production preferred)
kubectl apply -f manifest.yaml
kubectl apply -f ./k8s/                     # Directory
kubectl apply -f ./k8s/ --recursive        # Recursive
kubectl apply -f https://url/manifest.yaml
kubectl apply -k ./kustomize/              # Kustomize

# Dry run
kubectl apply -f manifest.yaml --dry-run=client   # Local validation
kubectl apply -f manifest.yaml --dry-run=server   # Server-side validation
kubectl diff -f manifest.yaml                      # What will change

# ============================================
# UPDATE
# ============================================
kubectl set image deployment/myapp myapp=myapp:2.0
kubectl set image deployment/myapp myapp=myapp:2.0 --record
kubectl set resources deployment myapp \
  --limits=cpu=500m,memory=512Mi \
  --requests=cpu=100m,memory=128Mi
kubectl set env deployment/myapp NODE_ENV=production
kubectl set env deployment/myapp --from=configmap/myconfig
kubectl set serviceaccount deployment/myapp myapp-sa
kubectl patch deployment myapp -p \
  '{"spec":{"replicas":5}}'
kubectl patch pod mypod --type=json \
  -p='[{"op":"replace","path":"/spec/containers/0/image","value":"nginx:alpine"}]'
kubectl edit deployment myapp                # Editor mein open

# ============================================
# DELETE
# ============================================
kubectl delete pod mypod
kubectl delete pod mypod --grace-period=0 --force  # Immediate
kubectl delete pods -l app=myapp            # Label se
kubectl delete pods --all -n myns           # Namespace ke saare
kubectl delete -f manifest.yaml
kubectl delete deployment,service myapp     # Multiple types
kubectl delete namespace myns               # Namespace + sab kuch

# ============================================
# SCALE & ROLLOUT
# ============================================
kubectl scale deployment myapp --replicas=5
kubectl scale deployment myapp --current-replicas=3 --replicas=5
kubectl autoscale deployment myapp --cpu-percent=70 --min=2 --max=10

kubectl rollout status deployment/myapp
kubectl rollout status deployment/myapp --timeout=5m
kubectl rollout history deployment/myapp
kubectl rollout history deployment/myapp --revision=3
kubectl rollout undo deployment/myapp
kubectl rollout undo deployment/myapp --to-revision=2
kubectl rollout pause deployment/myapp
kubectl rollout resume deployment/myapp
kubectl rollout restart deployment/myapp    # All pods restart

# ============================================
# LOGS
# ============================================
kubectl logs mypod
kubectl logs mypod -c container-name       # Multi-container pod
kubectl logs -f mypod                      # Live follow
kubectl logs --tail=100 mypod
kubectl logs --since=1h mypod
kubectl logs --since-time="2024-01-01T00:00:00Z" mypod
kubectl logs --previous mypod              # Crashed pod ke logs
kubectl logs -l app=myapp                  # Label se (saare pods)
kubectl logs -l app=myapp --all-containers # Saare containers

# ============================================
# EXEC
# ============================================
kubectl exec -it mypod -- bash
kubectl exec -it mypod -- sh              # bash nahi hai toh
kubectl exec -it mypod -c container -- bash  # Specific container
kubectl exec mypod -- ls /app
kubectl exec mypod -- env
kubectl exec mypod -- cat /etc/config/app.properties

# ============================================
# PORT FORWARD
# ============================================
kubectl port-forward pod/mypod 8080:80
kubectl port-forward deployment/myapp 8080:3000
kubectl port-forward service/myservice 8080:80
kubectl port-forward --address 0.0.0.0 service/myservice 8080:80

# ============================================
# COPY FILES
# ============================================
kubectl cp mypod:/app/logs/app.log ./app.log
kubectl cp ./config.json mypod:/app/config.json
kubectl cp mypod:/app/logs ./local-logs -c container-name

# ============================================
# NODE OPERATIONS
# ============================================
kubectl get nodes
kubectl get nodes -o wide
kubectl describe node worker-1
kubectl top nodes                           # CPU/Memory (metrics-server chahiye)
kubectl top nodes --sort-by=memory
kubectl cordon worker-1                     # Scheduling band karo
kubectl uncordon worker-1                   # Scheduling resume
kubectl drain worker-1 --ignore-daemonsets --delete-emptydir-data
kubectl drain worker-1 --ignore-daemonsets --delete-emptydir-data \
  --grace-period=60 --timeout=120s

kubectl label node worker-1 disk=ssd
kubectl label node worker-1 disk-          # Label remove
kubectl taint node worker-1 special=true:NoSchedule
kubectl taint node worker-1 special-       # Taint remove

# ============================================
# NAMESPACE OPERATIONS
# ============================================
kubectl get namespaces
kubectl create namespace myapp
kubectl delete namespace myapp
kubectl config set-context --current --namespace=myapp  # Default ns change

# ============================================
# RESOURCE INFO
# ============================================
kubectl api-resources                       # All resource types
kubectl api-resources --namespaced=true     # Namespaced resources
kubectl api-resources --namespaced=false    # Cluster-wide resources
kubectl api-versions                        # All API versions
kubectl explain pod                         # Pod documentation
kubectl explain pod.spec
kubectl explain pod.spec.containers.resources

# ============================================
# EVENTS
# ============================================
kubectl get events
kubectl get events -n production
kubectl get events --sort-by='.lastTimestamp'
kubectl get events --field-selector type=Warning
kubectl get events --field-selector reason=BackOff
kubectl get events -A --sort-by='.lastTimestamp' | tail -20

# ============================================
# RESOURCE USAGE
# ============================================
kubectl top pods
kubectl top pods -A
kubectl top pods --sort-by=memory
kubectl top pods --sort-by=cpu
kubectl top pods --containers                # Container-level
kubectl top nodes

# ============================================
# USEFUL SHORTCUTS
# ============================================
# Common aliases
alias k=kubectl
alias kg='kubectl get'
alias kd='kubectl describe'
alias kl='kubectl logs'
alias ke='kubectl exec -it'
alias ka='kubectl apply -f'
alias kdel='kubectl delete'

# Resource short names
# po = pods, svc = services, deploy = deployments
# rs = replicasets, ds = daemonsets, sts = statefulsets
# cm = configmaps, ns = namespaces, no = nodes
# pv = persistentvolumes, pvc = persistentvolumeclaims
# ing = ingress, sa = serviceaccounts, ep = endpoints
# hpa = horizontalpodautoscalers, cj = cronjobs
```

---

## Chapter 5: Pods — Complete

> 🔗 Related: [Deployments](#chapter-10-deployments) | [Init Containers](#chapter-6-init-containers--sidecar-pattern) | [Labels](#chapter-7-labels-selectors--annotations)

### 🇮🇳 Hindi Explanation
**Pod** K8s ki smallest deployable unit hai. Ek pod mein ek ya zyada containers hote hain jo:
- Same **network namespace** share karte hain → localhost se communicate
- Same **storage volumes** share karte hain
- Sath mein **schedule** hote hain (same node pe)

**Important:** Pods directly use mat karo production mein — ek baar crash hua toh dobara nahi aayega. **Deployment** use karo!

```
Pod = Wrapper around containers
┌─────────────────────────────────────┐
│                Pod                   │
│  ┌──────────────┐  ┌─────────────┐  │
│  │  Container 1 │  │ Container 2 │  │
│  │  (main app)  │  │  (sidecar)  │  │
│  └──────────────┘  └─────────────┘  │
│  Shared Network (localhost:port)     │
│  Shared Volumes (/data, /logs)       │
│  Same Node                           │
└─────────────────────────────────────┘
```

### Complete Pod Manifest
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  namespace: production
  labels:
    app: myapp
    version: "2.0"
    environment: production
    tier: backend
  annotations:
    description: "Main application pod"
    git-commit: "abc123"
    prometheus.io/scrape: "true"
    prometheus.io/port: "3000"

spec:
  # ============================================
  # CONTAINERS
  # ============================================
  containers:
  - name: main-app
    image: myapp:2.0
    imagePullPolicy: Always           # Always | IfNotPresent | Never

    ports:
    - name: http
      containerPort: 3000
      protocol: TCP
    - name: metrics
      containerPort: 9090

    # Environment Variables
    env:
    - name: NODE_ENV
      value: "production"
    - name: POD_NAME
      valueFrom:
        fieldRef:
          fieldPath: metadata.name    # Downward API
    - name: POD_IP
      valueFrom:
        fieldRef:
          fieldPath: status.podIP
    - name: CPU_REQUEST
      valueFrom:
        resourceFieldRef:
          resource: requests.cpu
    - name: DB_HOST
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: database_host
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: app-secrets
          key: db_password

    envFrom:
    - configMapRef:
        name: app-config
        optional: true
    - secretRef:
        name: app-secrets

    # Resources (MANDATORY in production!)
    resources:
      requests:
        memory: "128Mi"               # Scheduler ko batao minimum
        cpu: "250m"                   # 250 millicores = 0.25 CPU
        ephemeral-storage: "1Gi"
      limits:
        memory: "512Mi"               # Exceed kiya → OOMKilled
        cpu: "500m"                   # Exceed kiya → Throttled
        ephemeral-storage: "2Gi"

    # Volume Mounts
    volumeMounts:
    - name: app-data
      mountPath: /app/data
    - name: app-config
      mountPath: /app/config
      readOnly: true
    - name: tmp-dir
      mountPath: /tmp
    - name: app-secrets
      mountPath: /run/secrets
      readOnly: true

    # Health Checks (TEENO important hain!)
    livenessProbe:                    # Container alive hai? → Fail = restart
      httpGet:
        path: /health/live
        port: 3000
        httpHeaders:
        - name: X-Health-Check
          value: "kubernetes"
      initialDelaySeconds: 30         # Container start hone ke baad wait karo
      periodSeconds: 20               # Har 20 sec mein check
      timeoutSeconds: 5               # 5 sec mein respond nahi → fail
      failureThreshold: 3             # 3 consecutive failures → restart
      successThreshold: 1

    readinessProbe:                   # Traffic receive karne ready? → Fail = endpoint remove
      httpGet:
        path: /health/ready
        port: 3000
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 3
      failureThreshold: 3
      successThreshold: 1

    startupProbe:                     # Slow start apps ke liye → Fail = restart
      httpGet:
        path: /health/started
        port: 3000
      failureThreshold: 30            # 30 * 10s = 5 minutes wait
      periodSeconds: 10

    # Lifecycle hooks
    lifecycle:
      postStart:                      # Container start hone ke baad
        exec:
          command: ["/bin/sh", "-c", "echo started >> /tmp/start.log"]
      preStop:                        # Container stop hone se pehle
        exec:
          command: ["/bin/sh", "-c", "sleep 15 && nginx -s quit"]
        # Load balancer se remove hone ka time

    # Security Context (container level)
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      runAsNonRoot: true
      runAsUser: 1000
      runAsGroup: 1000
      capabilities:
        drop: ["ALL"]
        add: ["NET_BIND_SERVICE"]     # Port 80 bind ke liye

  # ============================================
  # INIT CONTAINERS
  # ============================================
  initContainers:
  - name: wait-for-db
    image: busybox:1.36
    command: ['sh', '-c',
      'until nc -z postgres-service 5432;
       do echo "Waiting for database..."; sleep 3; done;
       echo "Database is ready!"']
  - name: db-migrate
    image: myapp:2.0
    command: ["npm", "run", "migrate"]
    env:
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: app-secrets
          key: db_password

  # ============================================
  # VOLUMES
  # ============================================
  volumes:
  - name: app-data
    persistentVolumeClaim:
      claimName: myapp-pvc
  - name: app-config
    configMap:
      name: app-config
      items:
      - key: app.properties
        path: app.properties
        mode: 0444
  - name: app-secrets
    secret:
      secretName: app-secrets
      defaultMode: 0400
  - name: tmp-dir
    emptyDir:
      medium: ""                      # "" = disk, "Memory" = RAM
      sizeLimit: 500Mi
  - name: shared-data
    emptyDir: {}                      # Containers ke beech share
  - name: host-logs
    hostPath:
      path: /var/log/myapp
      type: DirectoryOrCreate

  # ============================================
  # SCHEDULING
  # ============================================
  nodeSelector:
    kubernetes.io/os: linux
    disk: ssd

  restartPolicy: Always               # Always | OnFailure | Never
  terminationGracePeriodSeconds: 60
  serviceAccountName: myapp-sa
  automountServiceAccountToken: false

  # Security Context (pod level)
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    fsGroup: 2000                     # Volume ownership
    seccompProfile:
      type: RuntimeDefault

  # DNS Config
  dnsPolicy: ClusterFirst             # ClusterFirst | Default | None
  dnsConfig:
    nameservers:
    - 1.1.1.1
    options:
    - name: ndots
      value: "2"

  # Image Pull Secrets (Private registry)
  imagePullSecrets:
  - name: registry-credentials

  # Priority
  priorityClassName: high-priority

  # Topology Spread (HA ke liye)
  topologySpreadConstraints:
  - maxSkew: 1
    topologyKey: kubernetes.io/hostname
    whenUnsatisfiable: DoNotSchedule
    labelSelector:
      matchLabels:
        app: myapp
```

### Probe Types
```yaml
# HTTP Probe
livenessProbe:
  httpGet:
    path: /health
    port: 3000
    scheme: HTTPS

# TCP Probe
livenessProbe:
  tcpSocket:
    port: 5432

# Exec Probe
livenessProbe:
  exec:
    command:
    - sh
    - -c
    - "redis-cli ping | grep PONG"

# gRPC Probe (K8s 1.24+)
livenessProbe:
  grpc:
    port: 50051
    service: health.HealthService
```

### 🎯 Interview Answer
**Q: What is the difference between liveness, readiness, and startup probes?**

**Liveness Probe**: checks if the container is alive. Failure → K8s **restarts** the container. Use case: detect deadlocks where process runs but can't progress (infinite loop, etc.). **Readiness Probe**: checks if container is ready to serve traffic. Failure → pod **removed from Service endpoints** (traffic stops, no restart). Use case: during initialization, DB connection, cache warming — don't send traffic until ready. **Startup Probe**: for slow-starting applications. While it's running, liveness and readiness are disabled. Failure after `failureThreshold * periodSeconds` → container restart. Use case: legacy apps taking >120s to start. All three have: `initialDelaySeconds`, `periodSeconds`, `timeoutSeconds`, `failureThreshold`, `successThreshold`.

---

## Chapter 6: Init Containers & Sidecar Pattern

> 🔗 Related: [Pods](#chapter-5-pods--complete) | [DaemonSets](#chapter-20-daemonsets)

### 🇮🇳 Hindi Explanation

**Init Containers:**
- Main containers se pehle run karte hain
- Ek ke baad ek (sequential) run karte hain
- Saare complete hone ke baad main containers start hote hain
- Use: wait for dependencies, setup tasks, DB migrations

**Sidecar Pattern:**
- Main container ke saath same pod mein helper container
- Use: logging, monitoring, proxy, config refresh

```yaml
# ============================================
# INIT CONTAINERS — Complete Example
# ============================================
apiVersion: v1
kind: Pod
spec:
  initContainers:
  # Init 1: Wait for database
  - name: wait-for-postgres
    image: postgres:15-alpine
    command:
    - sh
    - -c
    - |
      until pg_isready -h postgres-service -p 5432 -U admin; do
        echo "Waiting for PostgreSQL..."
        sleep 2
      done
      echo "PostgreSQL is ready!"

  # Init 2: Wait for Redis
  - name: wait-for-redis
    image: redis:7-alpine
    command:
    - sh
    - -c
    - |
      until redis-cli -h redis-service ping | grep PONG; do
        echo "Waiting for Redis..."
        sleep 2
      done

  # Init 3: Database migration
  - name: db-migrate
    image: myapp:2.0
    command: ["npm", "run", "db:migrate"]
    env:
    - name: DATABASE_URL
      valueFrom:
        secretKeyRef:
          name: app-secrets
          key: database_url

  # Init 4: Download config from S3
  - name: download-config
    image: amazon/aws-cli:latest
    command:
    - sh
    - -c
    - aws s3 cp s3://mybucket/config.json /shared/config.json
    volumeMounts:
    - name: shared-config
      mountPath: /shared

  containers:
  - name: main-app
    image: myapp:2.0
    volumeMounts:
    - name: shared-config
      mountPath: /app/config

  volumes:
  - name: shared-config
    emptyDir: {}

# ============================================
# SIDECAR PATTERN — Logging Example
# ============================================
apiVersion: v1
kind: Pod
spec:
  containers:
  # Main app
  - name: app
    image: myapp:2.0
    volumeMounts:
    - name: app-logs
      mountPath: /var/log/app

  # Sidecar 1: Log shipper
  - name: log-shipper
    image: fluent/fluent-bit:latest
    volumeMounts:
    - name: app-logs
      mountPath: /var/log/app
      readOnly: true
    - name: fluent-bit-config
      mountPath: /fluent-bit/etc/

  # Sidecar 2: Metrics exporter
  - name: metrics-exporter
    image: prom/node-exporter:latest
    ports:
    - containerPort: 9100

  # Sidecar 3: Envoy proxy (Service Mesh)
  - name: envoy-proxy
    image: envoyproxy/envoy:latest
    ports:
    - containerPort: 9901   # Admin
    - containerPort: 15001  # Outbound proxy

  volumes:
  - name: app-logs
    emptyDir: {}
  - name: fluent-bit-config
    configMap:
      name: fluent-bit-config

# ============================================
# AMBASSADOR PATTERN (Proxy sidecar)
# ============================================
# Main app → localhost:6379 pe connect karo
# Ambassador sidecar localhost:6379 → actual Redis cluster route karta hai
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: app
    image: myapp:2.0
    env:
    - name: REDIS_HOST
      value: "localhost"    # Ambassador ko
    - name: REDIS_PORT
      value: "6379"
  - name: redis-ambassador
    image: haproxy:latest
    # HAProxy config: localhost:6379 → redis-cluster:6379

# ============================================
# ADAPTER PATTERN (Output transformer)
# ============================================
# App → custom log format
# Adapter → standard format transform
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: app
    image: legacy-app:1.0
    volumeMounts:
    - name: logs
      mountPath: /var/log/app
  - name: log-adapter
    image: log-transformer:latest
    # Reads custom format, outputs JSON
    volumeMounts:
    - name: logs
      mountPath: /var/log/input
      readOnly: true
  volumes:
  - name: logs
    emptyDir: {}
```

### 🎯 Interview Answer
**Q: What are init containers and sidecar containers?**

**Init containers** run and complete before the main containers start. They run sequentially (not parallel). Use cases: waiting for dependencies (DB, message queue), pre-configuration tasks, DB migrations, downloading configuration. If an init container fails, the pod restarts. **Sidecar containers** run alongside the main container in the same pod, sharing network and volumes. Common patterns: **Sidecar** (log shipping with Fluentd), **Ambassador** (proxy to external services via localhost), **Adapter** (transform app output to standard format). In K8s 1.29+, there's a native sidecar container concept with `restartPolicy: Always` in initContainers, which ensures the sidecar runs throughout the pod lifecycle.

---

## Chapter 7: Labels, Selectors & Annotations

> 🔗 Related: [Pods](#chapter-5-pods--complete) | [Services](#chapter-11-services) | [Deployments](#chapter-10-deployments)

### 🇮🇳 Hindi Explanation
**Labels** — Objects pe key-value pairs. **K8s internally use karta hai** selection ke liye.
**Annotations** — Extra metadata. **Humans aur tools** ke liye.
**Selectors** — Labels ke basis pe objects filter/select karo.

```yaml
# ============================================
# LABELS — Standard + Custom
# ============================================
metadata:
  labels:
    # Kubernetes recommended labels
    app.kubernetes.io/name: "myapp"
    app.kubernetes.io/instance: "myapp-production"
    app.kubernetes.io/version: "2.1.0"
    app.kubernetes.io/component: "frontend"
    app.kubernetes.io/part-of: "ecommerce"
    app.kubernetes.io/managed-by: "helm"

    # Custom labels
    environment: "production"
    team: "backend"
    tier: "web"
    release: "stable"
    cost-center: "engineering"
    criticality: "high"

  # ============================================
  # ANNOTATIONS
  # ============================================
  annotations:
    # Human-readable info
    description: "Main web application"
    owner: "backend-team@company.com"
    documentation: "https://docs.company.com/myapp"
    support-slack: "#backend-support"

    # CI/CD info
    git-commit: "abc123def456"
    git-branch: "main"
    build-date: "2024-01-15T10:30:00Z"
    build-number: "42"
    deployed-by: "github-actions"

    # Tool configurations
    prometheus.io/scrape: "true"          # Prometheus scraping
    prometheus.io/port: "9090"
    prometheus.io/path: "/metrics"
    kubernetes.io/ingress.class: "nginx"  # Ingress config
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
    backup.velero.io/backup-volumes: "data"
```

```bash
# ============================================
# LABEL OPERATIONS
# ============================================
# Filter karo
kubectl get pods -l app=myapp
kubectl get pods -l environment=production
kubectl get pods -l "environment in (production,staging)"
kubectl get pods -l "environment notin (development)"
kubectl get pods -l app=myapp,environment=production   # AND
kubectl get pods -l '!deprecated'                      # Key absent
kubectl get pods --show-labels                         # Labels dikhao
kubectl get pods -L app,environment                    # Specific labels as columns

# Add/Update/Remove
kubectl label pod mypod version=2.0
kubectl label pod mypod version=2.1 --overwrite
kubectl label pod mypod version-                       # Remove

kubectl label node worker-1 disk=ssd
kubectl label node worker-1 region=us-east
kubectl label node worker-1 gpu=true

kubectl annotate pod mypod description="My app"
kubectl annotate pod mypod description="Updated" --overwrite
kubectl annotate pod mypod description-                # Remove

# Bulk operations
kubectl label pods -l app=myapp environment=production
kubectl label pods --all version=1.0 -n myns
```

```yaml
# ============================================
# SELECTOR TYPES
# ============================================

# Equality-based (Service, ReplicationController mein)
selector:
  app: myapp
  environment: production

# Set-based (Deployment, ReplicaSet, Job mein)
selector:
  matchLabels:
    app: myapp
  matchExpressions:
  - key: environment
    operator: In              # In | NotIn | Exists | DoesNotExist
    values: ["production", "staging"]
  - key: tier
    operator: Exists          # Key exist karna chahiye (value kaisi bhi)
  - key: deprecated
    operator: DoesNotExist    # Key nahi honi chahiye
  - key: version
    operator: NotIn
    values: ["v0.1", "v0.2"]
```

### 🎯 Interview Answer
**Q: What is the difference between Labels and Annotations?**

**Labels** are key-value pairs used to identify and select Kubernetes objects. They are used by selectors — Services find their pods via label selectors, Deployments manage ReplicaSets via labels, schedulers use labels for node selection. Labels have constraints: keys/values must be 63 chars max, only alphanumeric, `-`, `_`, `.`. **Annotations** store arbitrary metadata not used for selection — build info, git SHAs, documentation URLs, tool configurations (Prometheus, cert-manager). Annotations can hold larger, more complex data. Rule: if you need to query or select objects by it → Label. If it's metadata for humans/tools → Annotation.

---

## Chapter 8: Namespaces

> 🔗 Related: [RBAC](#chapter-22-rbac) | [ResourceQuota](#chapter-17-resourcequota--limitrange) | [Network Policies](#chapter-24-network-policies)

### 🇮🇳 Hindi Explanation
**Namespace** ek virtual cluster hai ek physical cluster ke andar. Different teams ya environments ke resources isolate karo.

```
K8s Cluster
├── default          (User workloads by default)
├── kube-system      (K8s system components)
├── kube-public      (Public readable resources)
├── kube-node-lease  (Node heartbeats)
└── Custom:
    ├── production   (Prod workloads)
    ├── staging      (Staging environment)
    ├── development  (Dev workloads)
    └── monitoring   (Prometheus, Grafana)
```

```yaml
# ============================================
# NAMESPACE CREATE
# ============================================
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    environment: production
    team: backend
  annotations:
    description: "Production workloads"
    contact: "backend-team@company.com"
```

```bash
# Namespace Commands
kubectl get namespaces
kubectl get ns
kubectl create namespace production
kubectl describe namespace production
kubectl delete namespace production     # Namespace + sab kuch delete!

# Namespace mein kaam karo
kubectl get pods -n production
kubectl apply -f manifest.yaml -n production
kubectl config set-context --current --namespace=production  # Default change

# Kubens tool (faster namespace switch)
kubens production                       # Switch
kubens                                  # Current
```

### Namespace Isolation
```yaml
# ============================================
# RESOURCE QUOTA (Namespace ki resource cap)
# ============================================
apiVersion: v1
kind: ResourceQuota
metadata:
  name: production-quota
  namespace: production
spec:
  hard:
    # Compute
    requests.cpu: "20"
    requests.memory: 40Gi
    limits.cpu: "40"
    limits.memory: 80Gi

    # Objects
    pods: "100"
    services: "50"
    persistentvolumeclaims: "30"
    secrets: "100"
    configmaps: "100"
    replicationcontrollers: "0"        # Disable old RC

    # Service types
    services.loadbalancers: "5"
    services.nodeports: "10"

    # Storage
    requests.storage: "500Gi"
    fast-ssd.storageclass.storage.k8s.io/requests.storage: "200Gi"

    # Extended resources
    count/deployments.apps: "30"
    count/statefulsets.apps: "10"
    count/jobs.batch: "20"

---
# ============================================
# LIMIT RANGE (Default + Max per container)
# ============================================
apiVersion: v1
kind: LimitRange
metadata:
  name: production-limitrange
  namespace: production
spec:
  limits:
  - type: Container
    default:                          # Specify nahi kiya toh ye use hoga
      cpu: 500m
      memory: 256Mi
    defaultRequest:
      cpu: 100m
      memory: 128Mi
    max:
      cpu: "4"
      memory: 4Gi
    min:
      cpu: 50m
      memory: 64Mi
    maxLimitRequestRatio:             # limit/request max ratio
      cpu: "10"
      memory: "4"

  - type: Pod
    max:
      cpu: "8"
      memory: 8Gi

  - type: PersistentVolumeClaim
    max:
      storage: 50Gi
    min:
      storage: 100Mi
```

### Cluster-scoped vs Namespace-scoped
```bash
# Namespace-scoped (namespace mein rahte hain)
# pods, services, deployments, configmaps, secrets, pvc,
# serviceaccounts, roles, rolebindings, ingresses, hpa...

# Cluster-scoped (poore cluster mein)
# nodes, persistentvolumes, namespaces, clusterroles,
# clusterrolebindings, storageclasses, ingressclasses...

kubectl api-resources --namespaced=true    # Namespaced resources
kubectl api-resources --namespaced=false   # Cluster-wide
```

---

## Chapter 9: ReplicaSets

> 🔗 Related: [Deployments](#chapter-10-deployments) | [Labels](#chapter-7-labels-selectors--annotations)

### 🇮🇳 Hindi Explanation
**ReplicaSet** ensure karta hai ki desired number of pod replicas hamesha running rahe. Agar pod crash hua → automatically new pod create karta hai.

**Directly use mat karo** — Deployment ke through use karo. Deployment rolling updates, rollback provide karta hai.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-rs
  namespace: production
  labels:
    app: myapp
spec:
  replicas: 3                         # Desired state

  selector:
    matchLabels:
      app: myapp
    matchExpressions:
    - key: environment
      operator: In
      values: [production]

  template:                           # Pod template
    metadata:
      labels:
        app: myapp                    # selector se match karna ZAROORI
        environment: production
    spec:
      containers:
      - name: myapp
        image: myapp:1.0
        ports:
        - containerPort: 3000
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

```bash
kubectl get rs
kubectl get rs -n production
kubectl describe rs myapp-rs
kubectl scale rs myapp-rs --replicas=5
kubectl delete rs myapp-rs                  # RS + pods delete
kubectl delete rs myapp-rs --cascade=orphan # Sirf RS delete, pods raho
```

### ReplicaSet vs Deployment
```
ReplicaSet:                  Deployment:
- Specific replicas maintain  - RS manage karta hai
- No rolling update           - Rolling updates ✅
- No rollback                 - Rollback ✅
- Direct use (legacy)         - Recommended approach
```

---

## Chapter 10: Deployments

> 🔗 Related: [ReplicaSets](#chapter-9-replicasets) | [HPA](#chapter-25-horizontal-pod-autoscaler-hpa) | [Services](#chapter-11-services)

### 🇮🇳 Hindi Explanation
**Deployment** K8s ka sabse commonly used resource hai applications ke liye. Hierarchy:
```
Deployment → manages → ReplicaSet → manages → Pods
```

### Complete Deployment Manifest
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  namespace: production
  labels:
    app: myapp
  annotations:
    deployment.kubernetes.io/revision: "1"
    kubernetes.io/change-cause: "Update to version 2.0"

spec:
  replicas: 3

  # ============================================
  # SELECTOR
  # ============================================
  selector:
    matchLabels:
      app: myapp

  # ============================================
  # UPDATE STRATEGY
  # ============================================
  strategy:
    type: RollingUpdate               # RollingUpdate | Recreate
    rollingUpdate:
      maxSurge: 1                     # Extra pods during update (number or %)
      maxUnavailable: 0               # Unavailable pods allowed (number or %)
      # maxSurge: 25% → replicas ke 25% extra
      # maxUnavailable: 25% → replicas ke 25% unavailable ok

  # Recreate strategy → sab band, phir naya start (downtime hoga!)
  # strategy:
  #   type: Recreate

  revisionHistoryLimit: 10            # Kitne old RS rakhne hain (rollback ke liye)
  progressDeadlineSeconds: 600        # 10 min mein complete nahi hua → failed

  # ============================================
  # POD TEMPLATE
  # ============================================
  template:
    metadata:
      labels:
        app: myapp
        version: "2.0"
    spec:
      containers:
      - name: myapp
        image: myapp:2.0
        ports:
        - containerPort: 3000
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 500m
            memory: 512Mi
        readinessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 10
          periodSeconds: 5
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 20
      terminationGracePeriodSeconds: 60
```

```bash
# ============================================
# DEPLOYMENT OPERATIONS
# ============================================
kubectl apply -f deployment.yaml

# Image update (rolling update trigger hogi)
kubectl set image deployment/myapp myapp=myapp:3.0
kubectl set image deployment/myapp myapp=myapp:3.0 --record

# Rollout manage karo
kubectl rollout status deployment/myapp        # Status dekho
kubectl rollout status deployment/myapp -w     # Watch
kubectl rollout history deployment/myapp       # History
kubectl rollout history deployment/myapp --revision=3  # Specific revision

# Rollback
kubectl rollout undo deployment/myapp          # Previous
kubectl rollout undo deployment/myapp --to-revision=2

# Pause/Resume (canary update ke liye)
kubectl rollout pause deployment/myapp
kubectl set image deployment/myapp myapp=myapp:3.0  # Sirf thoda update
kubectl rollout resume deployment/myapp

# Scale
kubectl scale deployment/myapp --replicas=10

# Restart (same image, new pods)
kubectl rollout restart deployment/myapp

# Force update (same image)
kubectl patch deployment myapp -p \
  '{"spec":{"template":{"metadata":{"annotations":{"date":"'$(date +%s)'"}}}}}'
```

### Update Strategies Explained
```
RollingUpdate (default):
Pods: [v1][v1][v1]
Step1: [v1][v1][v1][v2]  (maxSurge=1, ek extra)
Step2: [v1][v1][v2]      (ek purana band)
Step3: [v1][v2][v2][??]  wait for ready
Step4: [v2][v2][v2]      ✅ Complete

Recreate:
Pods: [v1][v1][v1]
Step1: []                (sab band → DOWNTIME!)
Step2: [v2][v2][v2]     ✅ Complete
```

### 🎯 Interview Answer
**Q: How does Kubernetes rolling update work and how do you rollback?**

When you update a Deployment (new image, env var, etc.), K8s creates a new ReplicaSet with updated pod template. It gradually scales up the new RS while scaling down the old RS, controlled by `maxSurge` (max extra pods) and `maxUnavailable` (max pods offline). New pods must pass `readinessProbe` before old ones are terminated — ensuring traffic always goes to healthy pods. If the update fails (pod crash, readiness failure), `kubectl rollout undo deployment/myapp` instantly reverts to the previous ReplicaSet (stored via `revisionHistoryLimit`). The rollback is instant because old RS still exists with scale 0. To rollback to specific version: `kubectl rollout undo deployment/myapp --to-revision=2`.

---

## Chapter 11: Services

> 🔗 Related: [Deployments](#chapter-10-deployments) | [Ingress](#chapter-12-ingress--ingressclass) | [DNS](#chapter-30-dns--service-discovery-coredns)

### 🇮🇳 Hindi Explanation
Pods ephemeral hain — restart hone pe IP change hota hai. **Service** ek stable endpoint provide karta hai jo pods ko abstract karta hai.

**Service kaise kaam karta hai:**
```
Client → Service (ClusterIP) → kube-proxy → Pod endpoints
         10.96.1.100:80           ↓
                             Load Balance
                          ┌──────────────┐
                          │  Pod1:3000   │
                          │  Pod2:3000   │
                          │  Pod3:3000   │
                          └──────────────┘
```

### All Service Types
```yaml
# ============================================
# 1. CLUSTERIP (Default — Internal only)
# ============================================
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
  namespace: production
spec:
  type: ClusterIP                     # Default
  selector:
    app: myapp
  ports:
  - name: http
    protocol: TCP
    port: 80                          # Service port (client ye use karta hai)
    targetPort: 3000                  # Pod port (ya named port)
  - name: https
    port: 443
    targetPort: 3443
  sessionAffinity: ClientIP           # Same client → same pod
  sessionAffinityConfig:
    clientIP:
      timeoutSeconds: 10800

---
# ============================================
# 2. NODEPORT (External — Node IP + Port)
# ============================================
apiVersion: v1
kind: Service
metadata:
  name: myapp-nodeport
spec:
  type: NodePort
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 3000
    nodePort: 30080                   # 30000-32767 range
    # Access: <ANY_NODE_IP>:30080

---
# ============================================
# 3. LOADBALANCER (Cloud LB — External)
# ============================================
apiVersion: v1
kind: Service
metadata:
  name: myapp-lb
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "nlb"
    service.beta.kubernetes.io/aws-load-balancer-internal: "false"
spec:
  type: LoadBalancer
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 3000
  loadBalancerSourceRanges:           # Allowed IPs
  - "10.0.0.0/8"
  - "203.0.113.0/24"
  externalTrafficPolicy: Local        # Cluster | Local
  # Local = Same node pe pod nahi hai toh route mat karo

---
# ============================================
# 4. EXTERNALNAME (DNS alias)
# ============================================
apiVersion: v1
kind: Service
metadata:
  name: external-db
spec:
  type: ExternalName
  externalName: prod-db.company.com   # CNAME DNS entry
  # kubectl exec app -- curl http://external-db → prod-db.company.com

---
# ============================================
# 5. HEADLESS SERVICE (No ClusterIP)
# ============================================
apiVersion: v1
kind: Service
metadata:
  name: myapp-headless
spec:
  clusterIP: None                     # Headless!
  selector:
    app: myapp
  ports:
  - port: 3000
  # DNS → Pod IPs directly return karta hai
  # StatefulSets ke liye use hota hai

---
# ============================================
# 6. EXTERNAL IP
# ============================================
apiVersion: v1
kind: Service
metadata:
  name: myapp-external-ip
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 3000
  externalIPs:
  - 203.0.113.100                     # Manually manage IP
```

```bash
# Service operations
kubectl get services
kubectl get svc
kubectl get svc -A
kubectl describe svc myapp-service
kubectl get endpoints myapp-service    # Actual pod IPs dekho
kubectl get ep                         # Short form

# Service expose karo
kubectl expose deployment myapp --port=80 --target-port=3000
kubectl expose deployment myapp --type=NodePort --port=80

# Service access test
kubectl run test --rm -it --image=busybox -- wget -O- http://myapp-service:80
```

### 🎯 Interview Answer
**Q: What are the different types of Kubernetes Services and when to use each?**

**ClusterIP** (default): virtual IP only within cluster, for internal microservice communication. **NodePort**: exposes service on every node's IP at a static port (30000-32767), accessible externally via `<NodeIP>:<NodePort>` — not recommended for production (no SSL, manual node IP management). **LoadBalancer**: provisions cloud provider's load balancer (AWS ELB, GCP LB) with public IP — production external traffic. **ExternalName**: CNAME DNS alias to external service, useful for pointing to external databases with K8s service name. **Headless** (ClusterIP: None): no virtual IP, DNS returns pod IPs directly — essential for StatefulSets where you need to address individual pods. Production recommendation: LoadBalancer + Ingress for HTTP/HTTPS.

---

## Chapter 12: Ingress & IngressClass

> 🔗 Related: [Services](#chapter-11-services) | [Network Policies](#chapter-24-network-policies)

### 🇮🇳 Hindi Explanation
5 services ke liye 5 LoadBalancers = bahut costly. **Ingress** ek single entry point se multiple services pe traffic route karta hai — path ya hostname ke basis pe.

```
Internet
    │
    ▼
Ingress Controller (nginx/traefik/etc.)
    │
    ├── /api/* → api-service:3000
    ├── /web/* → frontend-service:80
    └── admin.example.com → admin-service:8080
```

### Ingress Controller Install
```bash
# Nginx Ingress Controller
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.2/deploy/static/provider/cloud/deploy.yaml

# Verify
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx

# Minikube pe
minikube addons enable ingress
```

### Complete Ingress Manifest
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
  namespace: production
  annotations:
    # Nginx annotations
    kubernetes.io/ingress.class: "nginx"
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rate-limit: "100"
    nginx.ingress.kubernetes.io/rate-limit-window: "1m"
    nginx.ingress.kubernetes.io/proxy-body-size: "50m"
    nginx.ingress.kubernetes.io/proxy-read-timeout: "60"
    nginx.ingress.kubernetes.io/proxy-connect-timeout: "60"
    nginx.ingress.kubernetes.io/enable-cors: "true"
    nginx.ingress.kubernetes.io/cors-allow-origin: "https://myapp.com"

    # Auth
    nginx.ingress.kubernetes.io/auth-url: "https://auth.example.com/verify"

    # Cert-manager (automatic TLS)
    cert-manager.io/cluster-issuer: "letsencrypt-prod"

spec:
  ingressClassName: nginx             # IngressClass specify karo

  # TLS Configuration
  tls:
  - hosts:
    - myapp.example.com
    - api.example.com
    secretName: myapp-tls            # cert-manager automatically banayega

  rules:
  # Host-based routing
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix             # Prefix | Exact | ImplementationSpecific
        backend:
          service:
            name: frontend-service
            port:
              number: 80
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 3000
      - path: /api/v2/(.*)
        pathType: ImplementationSpecific  # Regex (nginx specific)
        backend:
          service:
            name: api-v2-service
            port:
              number: 3000

  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 3000

  - host: admin.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: admin-service
            port:
              number: 8080

---
# ============================================
# INGRESSCLASS
# ============================================
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: nginx
  annotations:
    ingressclass.kubernetes.io/is-default-class: "true"  # Default class
spec:
  controller: k8s.io/ingress-nginx

---
# ============================================
# CERT-MANAGER (Automatic TLS)
# ============================================
# Install:
# kubectl apply -f https://github.com/cert-manager/cert-manager/releases/latest/download/cert-manager.yaml

# ClusterIssuer (Let's Encrypt)
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: admin@example.com
    privateKeySecretRef:
      name: letsencrypt-prod-key
    solvers:
    - http01:
        ingress:
          class: nginx
```

---

## Chapter 13: ConfigMaps & Secrets

> 🔗 Related: [Pods](#chapter-5-pods--complete) | [RBAC](#chapter-22-rbac)

### 🇮🇳 Hindi Explanation
**ConfigMap** — Non-sensitive configuration (DB host, app settings, config files).
**Secret** — Sensitive data (passwords, tokens, certificates) — base64 encoded hote hain.

**Why?** Application config ko image se alag rakhte hain — ek image, alag configs!

```yaml
# ============================================
# CONFIGMAP — All forms
# ============================================
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: production
data:
  # Simple key-value
  APP_ENV: "production"
  LOG_LEVEL: "info"
  DATABASE_HOST: "postgres-service"
  DATABASE_PORT: "5432"
  MAX_POOL_SIZE: "20"
  CACHE_TTL: "3600"

  # Config file as value
  app.properties: |
    spring.datasource.url=jdbc:postgresql://postgres-service:5432/mydb
    spring.datasource.username=admin
    spring.cache.type=redis
    spring.redis.host=redis-service
    server.port=8080

  nginx.conf: |
    upstream backend {
      server api-service:3000;
    }
    server {
      listen 80;
      location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
      }
      location /health {
        return 200 "OK";
      }
    }

  # JSON config
  app-config.json: |
    {
      "database": {
        "host": "postgres-service",
        "port": 5432
      },
      "features": {
        "newUI": true,
        "darkMode": false
      }
    }

binaryData:                           # Binary data (base64 encoded)
  logo.png: <base64-encoded-content>

---
# ============================================
# SECRET — All types
# ============================================
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
  namespace: production
type: Opaque                          # Generic secrets

# Option 1: Pre-encoded (echo -n "value" | base64)
data:
  db_password: bXlzZWNyZXRwYXNzd29yZA==
  api_key: c2VjcmV0YXBpa2V5

# Option 2: Plain text (kubectl auto-encode karta hai)
stringData:
  db_username: "admin"
  db_password: "mysecretpassword"     # stringData data ko override karta hai
  connection_string: "postgresql://admin:mysecretpassword@postgres:5432/mydb"

---
# TLS Secret
apiVersion: v1
kind: Secret
metadata:
  name: myapp-tls
type: kubernetes.io/tls
data:
  tls.crt: <base64-certificate>
  tls.key: <base64-private-key>

---
# Docker Registry Secret
apiVersion: v1
kind: Secret
metadata:
  name: registry-credentials
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: <base64-docker-config>

---
# SSH Auth Secret
apiVersion: v1
kind: Secret
metadata:
  name: ssh-key
type: kubernetes.io/ssh-auth
data:
  ssh-privatekey: <base64-private-key>

---
# Basic Auth Secret
apiVersion: v1
kind: Secret
metadata:
  name: basic-auth
type: kubernetes.io/basic-auth
stringData:
  username: admin
  password: password123
```

```yaml
# ============================================
# POD MEIN USE KARO
# ============================================
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: app
    image: myapp:2.0

    # Method 1: Individual ENV variables
    env:
    - name: APP_ENV
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: APP_ENV
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: app-secrets
          key: db_password
          optional: false           # True = missing ok

    # Method 2: All keys as ENV (bulk)
    envFrom:
    - configMapRef:
        name: app-config
        optional: true
    - secretRef:
        name: app-secrets

    # Method 3: Volume mount (files ban jaate hain)
    volumeMounts:
    - name: config-files
      mountPath: /etc/config
    - name: secret-files
      mountPath: /etc/secrets
      readOnly: true
    - name: single-config
      mountPath: /etc/nginx/nginx.conf
      subPath: nginx.conf           # Sirf ek file mount karo

  volumes:
  - name: config-files
    configMap:
      name: app-config
      defaultMode: 0644
      items:                        # Specific keys mount karo
      - key: nginx.conf
        path: nginx.conf
        mode: 0444
      - key: app.properties
        path: app.properties

  - name: secret-files
    secret:
      secretName: app-secrets
      defaultMode: 0400             # Strict permissions
      items:
      - key: db_password
        path: db_password

  - name: single-config
    configMap:
      name: app-config
```

```bash
# ============================================
# CONFIGMAP COMMANDS
# ============================================
kubectl create configmap app-config \
  --from-literal=APP_ENV=production \
  --from-literal=LOG_LEVEL=info

kubectl create configmap app-config \
  --from-file=config.properties       # File name = key
kubectl create configmap app-config \
  --from-file=mykey=config.properties # Custom key
kubectl create configmap app-config \
  --from-env-file=.env               # .env file
kubectl create configmap nginx-config \
  --from-file=./nginx/               # Directory ke saare files

kubectl get configmaps
kubectl describe configmap app-config
kubectl edit configmap app-config
kubectl delete configmap app-config

# ============================================
# SECRET COMMANDS
# ============================================
kubectl create secret generic app-secrets \
  --from-literal=db_password=mysecret \
  --from-literal=api_key=myapikey

kubectl create secret generic app-secrets \
  --from-file=db_password=./password.txt

kubectl create secret tls myapp-tls \
  --cert=tls.crt \
  --key=tls.key

kubectl create secret docker-registry registry-creds \
  --docker-server=myregistry.com \
  --docker-username=user \
  --docker-password=pass \
  --docker-email=user@example.com

kubectl get secrets
kubectl describe secret app-secrets    # Values nahi dikhata
kubectl get secret app-secrets -o yaml # Base64 encoded dikhata hai

# Decode karo
kubectl get secret app-secrets \
  -o jsonpath='{.data.db_password}' | base64 -d

kubectl delete secret app-secrets
```

### 🎯 Interview Answer
**Q: What are the limitations of Kubernetes Secrets?**

Kubernetes Secrets are base64 encoded (NOT encrypted) by default — anyone with etcd access can decode them. Limitations: 1) **Not encrypted at rest** by default — enable encryption with `EncryptionConfiguration`; 2) **RBAC required** to restrict who can read secrets; 3) **Stored in etcd** — secure etcd with TLS and encryption; 4) **Size limit** 1MB per Secret. Production recommendations: enable **encryption at rest** for etcd, use **RBAC** to restrict Secret access, integrate with **Vault** (HashiCorp), **AWS Secrets Manager**, or **GCP Secret Manager** using External Secrets Operator, use **Sealed Secrets** for GitOps workflows, and **audit log** all secret access.

---

## Chapter 14: Taints & Tolerations

> 🔗 Related: [Node Affinity](#chapter-15-node-affinity--pod-affinity) | [Pods](#chapter-5-pods--complete)

### 🇮🇳 Hindi Explanation
**Taint** — Node pe lagao: "Yahan special pods hi aao"
**Toleration** — Pod pe lagao: "Main us node pe ja sakta hun"

**Analogy:** Node = VIP lounge (tainted), Pod = VIP pass wala guest (tolerates)

```
Normal Pods → ❌ Cannot schedule on tainted nodes
Tolerated Pods → ✅ Can schedule on tainted nodes
```

### Taint Effects
| Effect | Meaning |
|--------|---------|
| `NoSchedule` | Naya pod schedule nahi hoga |
| `PreferNoSchedule` | Prefer nahi karega (guarantee nahi) |
| `NoExecute` | Naya pod nahi + existing evict bhi hoga |

```bash
# ============================================
# TAINTS — Node pe lagao
# ============================================
# Taint add karo
kubectl taint node worker-1 special=gpu:NoSchedule
kubectl taint node worker-1 env=production:NoSchedule
kubectl taint node worker-2 maintenance=true:NoExecute    # Existing pods evict
kubectl taint node master-1 node-role.kubernetes.io/master:NoSchedule

# Taint remove karo (minus sign)
kubectl taint node worker-1 special=gpu:NoSchedule-
kubectl taint node worker-1 env-

# All taints dekho
kubectl describe node worker-1 | grep Taints
```

```yaml
# ============================================
# TOLERATIONS — Pod mein lagao
# ============================================
apiVersion: v1
kind: Pod
spec:
  tolerations:
  # Exact match
  - key: "special"
    operator: "Equal"                 # Equal | Exists
    value: "gpu"
    effect: "NoSchedule"             # NoSchedule | PreferNoSchedule | NoExecute

  # Any value tolerate karo
  - key: "env"
    operator: "Exists"
    effect: "NoSchedule"

  # Eviction delay ke saath
  - key: "maintenance"
    operator: "Equal"
    value: "true"
    effect: "NoExecute"
    tolerationSeconds: 3600          # 1 hour ke baad evict karo

  # Saare taints tolerate karo
  - operator: "Exists"               # All taints

  # System taints (DaemonSets ke liye)
  - key: "node.kubernetes.io/not-ready"
    operator: "Exists"
    effect: "NoExecute"
    tolerationSeconds: 300
  - key: "node.kubernetes.io/unreachable"
    operator: "Exists"
    effect: "NoExecute"
    tolerationSeconds: 300
```

### Common System Taints
```bash
# K8s automatically add karta hai:
node.kubernetes.io/not-ready:NoExecute
node.kubernetes.io/unreachable:NoExecute
node.kubernetes.io/disk-pressure:NoSchedule
node.kubernetes.io/memory-pressure:NoSchedule
node.kubernetes.io/pid-pressure:NoSchedule
node.kubernetes.io/network-unavailable:NoSchedule
node.kubernetes.io/unschedulable:NoSchedule

# Master node taint
node-role.kubernetes.io/control-plane:NoSchedule
```

### 🎯 Interview Answer
**Q: What are Taints and Tolerations in Kubernetes?**

**Taints** are applied to nodes to repel pods — they prevent pods from being scheduled unless they explicitly tolerate the taint. **Tolerations** are applied to pods allowing them to be scheduled on tainted nodes. Three effects: `NoSchedule` (don't schedule new pods), `PreferNoSchedule` (try to avoid but not guaranteed), `NoExecute` (don't schedule + evict existing pods). Use cases: dedicated nodes for GPU workloads, separate prod/dev nodes, maintenance mode (NoExecute evicts all pods), spot/preemptible instances. Important distinction: taints/tolerations prevent unwanted scheduling but don't guarantee scheduling on specific nodes — use Node Affinity for that.

---

## Chapter 15: Node Affinity & Pod Affinity

> 🔗 Related: [Taints & Tolerations](#chapter-14-taints--tolerations) | [Pods](#chapter-5-pods--complete)

### 🇮🇳 Hindi Explanation
**Node Affinity** — Pod ko specific nodes pe schedule karo (labels ke basis pe)
**Pod Affinity** — Pod ko dusre pods ke paas schedule karo (co-location)
**Pod Anti-Affinity** — Pod ko dusre pods se door schedule karo (spread)

```yaml
apiVersion: v1
kind: Pod
spec:
  # ============================================
  # NODE SELECTOR (Simple version)
  # ============================================
  nodeSelector:
    disk: ssd
    region: us-east

  # ============================================
  # AFFINITY (Advanced version)
  # ============================================
  affinity:

    # --- NODE AFFINITY ---
    nodeAffinity:
      # Hard rule (must satisfy)
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/os
            operator: In
            values: ["linux"]
          - key: disk
            operator: In
            values: ["ssd", "nvme"]
          - key: gpu
            operator: Exists
        - matchExpressions:          # OR condition (multiple terms)
          - key: region
            operator: In
            values: ["us-east-1", "us-east-2"]

      # Soft rule (prefer, not mandatory)
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100                  # 1-100, zyada = zyada prefer
        preference:
          matchExpressions:
          - key: instance-type
            operator: In
            values: ["m5.xlarge", "m5.2xlarge"]
      - weight: 50
        preference:
          matchExpressions:
          - key: availability-zone
            operator: In
            values: ["us-east-1a"]

    # --- POD AFFINITY (Ek pod ke paas jao) ---
    podAffinity:
      # Same node pe schedule karo jahan cache pod hai
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchLabels:
            app: cache
        topologyKey: kubernetes.io/hostname  # Same node
        namespaces: ["production"]

      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 50
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: app
              operator: In
              values: ["database"]
          topologyKey: topology.kubernetes.io/zone  # Same zone

    # --- POD ANTI-AFFINITY (Dur jao) ---
    podAntiAffinity:
      # Different nodes pe spread karo (HA!)
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchLabels:
            app: myapp              # Same app ke pods alag nodes pe
        topologyKey: kubernetes.io/hostname

      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchLabels:
              app: myapp
          topologyKey: topology.kubernetes.io/zone  # Alag zones prefer

  # ============================================
  # TOPOLOGY SPREAD CONSTRAINTS (Better HA)
  # ============================================
  topologySpreadConstraints:
  - maxSkew: 1                       # Max imbalance allowed
    topologyKey: kubernetes.io/hostname
    whenUnsatisfiable: DoNotSchedule # DoNotSchedule | ScheduleAnyway
    labelSelector:
      matchLabels:
        app: myapp
  - maxSkew: 1
    topologyKey: topology.kubernetes.io/zone
    whenUnsatisfiable: ScheduleAnyway
    labelSelector:
      matchLabels:
        app: myapp
```

### 🎯 Interview Answer
**Q: What is the difference between Node Affinity and Taints/Tolerations?**

**Node Affinity** is about pods attracting themselves to specific nodes — "I want to run on nodes with SSD disk." It uses node labels. **Taints/Tolerations** are about nodes repelling pods — "Only GPU workloads allowed here." They use taint keys. Node Affinity is pod-centric (specified in pod spec), Taints are node-centric (applied to nodes). For HA, use **Pod Anti-Affinity** with `topologyKey: kubernetes.io/hostname` to ensure replicas spread across different nodes. **Topology Spread Constraints** (newer, preferred) provides more fine-grained control over how pods spread across topology domains (nodes, zones, regions).

---

## Chapter 16: Resource Requests & Limits

> 🔗 Related: [HPA](#chapter-25-horizontal-pod-autoscaler-hpa) | [ResourceQuota](#chapter-17-resourcequota--limitrange)

### 🇮🇳 Hindi Explanation
**Requests** — Guaranteed resources (scheduler decision)
**Limits** — Maximum allowed (enforced at runtime)

```
CPU Behavior:
- Request = Guaranteed minimum
- Limit = Maximum (throttled if exceeded, NOT killed)
- 1 CPU = 1000m (millicores)

Memory Behavior:
- Request = Guaranteed minimum
- Limit = Maximum (OOMKilled if exceeded!)
- 1Mi = 1 Mebibyte (1024 * 1024 bytes)
```

```yaml
resources:
  requests:
    cpu: "250m"               # 0.25 CPU
    memory: "256Mi"           # 256 Mebibytes
    ephemeral-storage: "1Gi"  # Local temporary storage
  limits:
    cpu: "500m"               # 0.5 CPU (throttled if more)
    memory: "512Mi"           # OOMKilled if more
    ephemeral-storage: "2Gi"

# QoS Classes (Quality of Service):
# Guaranteed = requests == limits (both set, equal)
# Burstable  = requests < limits (or only one set)
# BestEffort = neither set (evicted first!)
```

```bash
# Resource usage dekho
kubectl top pods
kubectl top pods --containers
kubectl top nodes
kubectl describe node worker-1 | grep -A 10 "Allocated resources"

# Resource requests graph
kubectl get pods -o custom-columns=\
  NAME:.metadata.name,\
  CPU-REQ:.spec.containers[0].resources.requests.cpu,\
  MEM-REQ:.spec.containers[0].resources.requests.memory,\
  CPU-LIM:.spec.containers[0].resources.limits.cpu,\
  MEM-LIM:.spec.containers[0].resources.limits.memory
```

---

## Chapter 17: ResourceQuota & LimitRange

> 🔗 Related: [Namespaces](#chapter-8-namespaces) | [Resource Requests](#chapter-16-resource-requests--limits)

### 🇮🇳 Hindi Explanation
**ResourceQuota** — Namespace level pe total resources cap karo (multi-tenant clusters ke liye)
**LimitRange** — Container/Pod level pe default aur max limits set karo

```yaml
# ============================================
# RESOURCEQUOTA
# ============================================
apiVersion: v1
kind: ResourceQuota
metadata:
  name: production-quota
  namespace: production
spec:
  hard:
    # Compute
    requests.cpu: "20"
    requests.memory: 40Gi
    limits.cpu: "40"
    limits.memory: 80Gi

    # Object counts
    pods: "100"
    services: "50"
    persistentvolumeclaims: "30"
    secrets: "100"
    configmaps: "100"

    # Service restrictions
    services.loadbalancers: "5"
    services.nodeports: "10"

    # Storage
    requests.storage: "500Gi"
    gold.storageclass.storage.k8s.io/requests.storage: "200Gi"
    silver.storageclass.storage.k8s.io/requests.storage: "300Gi"

    # Extended resources
    count/deployments.apps: "30"
    count/statefulsets.apps: "10"
    count/jobs.batch: "20"
    count/cronjobs.batch: "10"

---
# ============================================
# LIMITRANGE
# ============================================
apiVersion: v1
kind: LimitRange
metadata:
  name: production-limits
  namespace: production
spec:
  limits:
  - type: Container
    default:
      cpu: 500m
      memory: 256Mi
    defaultRequest:
      cpu: 100m
      memory: 128Mi
    max:
      cpu: "4"
      memory: 4Gi
    min:
      cpu: 50m
      memory: 64Mi
    maxLimitRequestRatio:
      cpu: "10"
      memory: "4"

  - type: Pod
    max:
      cpu: "8"
      memory: 8Gi
    min:
      cpu: 100m
      memory: 128Mi

  - type: PersistentVolumeClaim
    max:
      storage: 100Gi
    min:
      storage: 100Mi
```

```bash
kubectl get resourcequota -n production
kubectl describe resourcequota production-quota -n production
# Output dikhayega: Used vs Hard limits

kubectl get limitrange -n production
kubectl describe limitrange production-limits -n production
```

### 🎯 Interview Answer
**Q: Difference between ResourceQuota and LimitRange?**

**ResourceQuota** operates at **namespace level** — sets aggregate hard limits for total resource consumption in a namespace (total CPU, memory, pod count, service count). If quota exceeded → new resource creation rejected. **LimitRange** operates at **object level** — sets default requests/limits for containers that don't specify them, and enforces min/max boundaries per container/pod/PVC. They complement each other: LimitRange ensures every pod has resource requests defined (prerequisite for ResourceQuota tracking), ResourceQuota prevents any team from monopolizing cluster resources in multi-tenant environments.

---

## Chapter 18: Persistent Volumes

> 🔗 Related: [StatefulSets](#chapter-19-statefulsets) | [Storage](#chapter-18-persistent-volumes)

### 🇮🇳 Hindi Explanation
**3 objects:**
1. **StorageClass** — Storage ka type define karo (AWS EBS, GCP PD, etc.)
2. **PersistentVolume (PV)** — Admin banaata hai — actual storage
3. **PersistentVolumeClaim (PVC)** — Developer request karta hai — "mujhe 20GB chahiye"

**Dynamic vs Static provisioning:**
- **Static**: Admin manually PV banata hai
- **Dynamic**: StorageClass se automatically PV banta hai PVC request pe

```
Developer creates PVC → StorageClass → Cloud creates Volume → PV created → PVC bound
```

### Access Modes
| Mode | Short | Description |
|------|-------|-------------|
| ReadWriteOnce | RWO | Ek node read/write |
| ReadOnlyMany | ROX | Multiple nodes read |
| ReadWriteMany | RWX | Multiple nodes read/write |
| ReadWriteOncePod | RWOP | Ek pod read/write (K8s 1.22+) |

### Complete Storage Manifests
```yaml
# ============================================
# STORAGECLASS (Dynamic provisioning)
# ============================================
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-ssd
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"  # Default class
provisioner: kubernetes.io/aws-ebs    # AWS EBS
parameters:
  type: gp3
  iopsPerGB: "3000"
  throughput: "125"
  encrypted: "true"
  kmsKeyId: "arn:aws:kms:us-east-1:123:key/abc"
reclaimPolicy: Retain                 # Delete | Retain | Recycle
allowVolumeExpansion: true            # Online resize allowed
volumeBindingMode: WaitForFirstConsumer  # Immediate | WaitForFirstConsumer
mountOptions:
- debug
- discard

---
# GCP StorageClass
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standard-rwo
provisioner: pd.csi.storage.gke.io
parameters:
  type: pd-balanced
  replication-type: none
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer

---
# Local StorageClass
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer

---
# ============================================
# PERSISTENTVOLUME (Admin banata hai — Static)
# ============================================
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-production-001
  labels:
    type: fast-ssd
    env: production
spec:
  capacity:
    storage: 100Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain  # Delete | Retain | Recycle
  storageClassName: fast-ssd
  volumeMode: Filesystem               # Filesystem | Block

  # AWS EBS
  awsElasticBlockStore:
    volumeID: vol-0a1b2c3d4e5f6g7h8
    fsType: ext4

  # NFS
  # nfs:
  #   server: 192.168.1.100
  #   path: /exports/data

  # Local volume
  # local:
  #   path: /mnt/data
  # nodeAffinity:
  #   required:
  #     nodeSelectorTerms:
  #     - matchExpressions:
  #       - key: kubernetes.io/hostname
  #         operator: In
  #         values: [worker-1]

  claimRef:                            # Specific PVC se bind karo
    name: myapp-pvc
    namespace: production

---
# ============================================
# PERSISTENTVOLUMECLAIM (Developer request)
# ============================================
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myapp-pvc
  namespace: production
  labels:
    app: myapp
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: fast-ssd          # StorageClass specify
  volumeMode: Filesystem
  resources:
    requests:
      storage: 20Gi
  selector:                           # Specific PV choose karo
    matchLabels:
      type: fast-ssd
    matchExpressions:
    - key: env
      operator: In
      values: [production]

---
# Pod mein PVC mount karo
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: app
    image: myapp:2.0
    volumeMounts:
    - name: data-volume
      mountPath: /app/data
    - name: config-volume
      mountPath: /app/config
      subPath: myapp                  # Subdirectory mount
  volumes:
  - name: data-volume
    persistentVolumeClaim:
      claimName: myapp-pvc
      readOnly: false
  - name: config-volume
    persistentVolumeClaim:
      claimName: shared-config-pvc
```

```bash
# Storage Commands
kubectl get storageclass (sc)
kubectl get persistentvolumes (pv)
kubectl get persistentvolumeclaims (pvc)
kubectl get pvc -n production

kubectl describe pvc myapp-pvc -n production
# Status: Bound | Pending | Lost
# Bound = PV mil gaya
# Pending = Koi PV available nahi

# PVC expand karo (StorageClass mein allowVolumeExpansion: true ho)
kubectl patch pvc myapp-pvc -p '{"spec":{"resources":{"requests":{"storage":"50Gi"}}}}'

# PV manually reclaim karo
kubectl patch pv mypv -p '{"spec":{"claimRef": null}}'
```

---

## Chapter 19: StatefulSets

> 🔗 Related: [Deployments](#chapter-10-deployments) | [Persistent Volumes](#chapter-18-persistent-volumes) | [Services](#chapter-11-services)

### 🇮🇳 Hindi Explanation
**StatefulSet** stateful applications ke liye (databases, Kafka, Elasticsearch).

**Deployment se alag:**
- Stable, unique pod names: `postgres-0`, `postgres-1`, `postgres-2`
- Ordered deployment: `postgres-0` → `postgres-1` → `postgres-2`
- Stable DNS: `postgres-0.postgres-headless.production.svc.cluster.local`
- Har pod ka apna PVC (alag storage)

```yaml
# ============================================
# HEADLESS SERVICE (StatefulSet ke liye zaroori)
# ============================================
apiVersion: v1
kind: Service
metadata:
  name: postgres-headless
  namespace: production
spec:
  clusterIP: None                     # Headless!
  selector:
    app: postgres
  ports:
  - port: 5432

---
# ============================================
# STATEFULSET — Complete Example
# ============================================
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
  namespace: production
spec:
  serviceName: postgres-headless      # Headless service ka naam
  replicas: 3
  selector:
    matchLabels:
      app: postgres

  # Update Strategy
  updateStrategy:
    type: RollingUpdate
    rollingUpdate:
      partition: 0                    # 0 = sab update, 2 = sirf 2+ index update

  # Pod Management Policy
  podManagementPolicy: OrderedReady   # OrderedReady | Parallel

  template:
    metadata:
      labels:
        app: postgres
    spec:
      terminationGracePeriodSeconds: 60
      containers:
      - name: postgres
        image: postgres:15-alpine
        env:
        - name: POSTGRES_DB
          value: mydb
        - name: POSTGRES_USER
          value: admin
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: password
        - name: PGDATA
          value: /var/lib/postgresql/data/pgdata
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        ports:
        - containerPort: 5432
        resources:
          requests:
            cpu: 500m
            memory: 1Gi
          limits:
            cpu: "2"
            memory: 4Gi
        volumeMounts:
        - name: postgres-storage
          mountPath: /var/lib/postgresql/data
        - name: postgres-config
          mountPath: /etc/postgresql
        livenessProbe:
          exec:
            command:
            - sh
            - -c
            - pg_isready -U admin -d mydb
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          exec:
            command:
            - sh
            - -c
            - pg_isready -U admin -d mydb
          initialDelaySeconds: 5
          periodSeconds: 5

      volumes:
      - name: postgres-config
        configMap:
          name: postgres-config

  # ============================================
  # VOLUME CLAIM TEMPLATES (Har pod ka apna PVC)
  # ============================================
  volumeClaimTemplates:
  - metadata:
      name: postgres-storage
      labels:
        app: postgres
    spec:
      accessModes: [ReadWriteOnce]
      storageClassName: fast-ssd
      resources:
        requests:
          storage: 50Gi
```

```bash
# StatefulSet Commands
kubectl get statefulsets (sts)
kubectl get sts -n production
kubectl describe sts postgres -n production

# Scale (ordered: postgres-3 banayega, phir postgres-4)
kubectl scale sts postgres --replicas=5

# Delete (pods reverse order mein delete honge: 4→3→2→1→0)
kubectl delete sts postgres
kubectl delete sts postgres --cascade=orphan   # PVCs preserve

# Individual pod access (DNS se)
# postgres-0.postgres-headless.production.svc.cluster.local
kubectl exec -it postgres-0 -- psql -U admin mydb

# Pod rolling restart
kubectl rollout restart statefulset/postgres
kubectl rollout status statefulset/postgres
```

### 🎯 Interview Answer
**Q: When to use StatefulSet vs Deployment?**

**Deployment** is for **stateless** applications where all pods are identical and interchangeable — web servers, REST APIs, workers. Pods can be replaced arbitrarily. **StatefulSet** is for **stateful** applications requiring: **stable network identity** (pod-0, pod-1 names and stable DNS), **ordered deployment/scaling/deletion** (pod-0 before pod-1), **persistent storage per pod** (each pod gets its own PVC via volumeClaimTemplates). Use StatefulSets for: PostgreSQL, MySQL, MongoDB, Kafka, Elasticsearch, ZooKeeper, Redis Cluster. The headless service provides DNS records for each pod individually: `pod-0.service.namespace.svc.cluster.local`.

---

## Chapter 20: DaemonSets

> 🔗 Related: [Pods](#chapter-5-pods--complete) | [Taints & Tolerations](#chapter-14-taints--tolerations)

### 🇮🇳 Hindi Explanation
**DaemonSet** ensure karta hai ki har node pe exactly ek pod chale. Naya node add karo → automatically pod deploy ho jaata hai. Node remove karo → pod bhi chala jaata hai.

**Use cases:**
- Log collection (Fluentd, Filebeat)
- Monitoring agents (Prometheus Node Exporter, Datadog)
- Network plugins (Calico, Flannel, Cilium)
- Storage daemons (Ceph, GlusterFS)

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-log-collector
  namespace: logging
  labels:
    app: log-collector
spec:
  selector:
    matchLabels:
      app: log-collector

  updateStrategy:
    type: RollingUpdate               # RollingUpdate | OnDelete
    rollingUpdate:
      maxUnavailable: 1               # Ek baar mein 1 node update

  template:
    metadata:
      labels:
        app: log-collector
    spec:
      # Master nodes pe bhi chalao
      tolerations:
      - key: node-role.kubernetes.io/control-plane
        operator: Exists
        effect: NoSchedule
      - key: node.kubernetes.io/not-ready
        operator: Exists
        effect: NoExecute
      - key: node.kubernetes.io/disk-pressure
        operator: Exists
        effect: NoSchedule

      # High priority (system pods ki tarah)
      priorityClassName: system-node-critical

      containers:
      - name: fluentd
        image: fluent/fluent-bit:2.1
        resources:
          requests:
            cpu: 50m
            memory: 50Mi
          limits:
            cpu: 200m
            memory: 200Mi
        env:
        - name: NODE_NAME
          valueFrom:
            fieldRef:
              fieldPath: spec.nodeName
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
        - name: fluent-bit-config
          mountPath: /fluent-bit/etc/
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true

      hostNetwork: false
      hostPID: false

      volumes:
      - name: varlog
        hostPath:
          path: /var/log
          type: Directory
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
          type: Directory
      - name: fluent-bit-config
        configMap:
          name: fluent-bit-config

---
# Prometheus Node Exporter DaemonSet
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
  namespace: monitoring
spec:
  selector:
    matchLabels:
      app: node-exporter
  template:
    metadata:
      labels:
        app: node-exporter
    spec:
      hostNetwork: true
      hostPID: true
      tolerations:
      - operator: Exists
      containers:
      - name: node-exporter
        image: prom/node-exporter:latest
        args:
        - --path.sysfs=/host/sys
        - --path.rootfs=/host/root
        - --no-collector.wifi
        - --no-collector.hwmon
        - --collector.filesystem.ignored-mount-points=^/(dev|proc|sys|var/lib/docker)($|/)
        ports:
        - containerPort: 9100
          hostPort: 9100
        volumeMounts:
        - name: sys
          mountPath: /host/sys
          readOnly: true
        - name: root
          mountPath: /host/root
          readOnly: true
      volumes:
      - name: sys
        hostPath:
          path: /sys
      - name: root
        hostPath:
          path: /
```

---

## Chapter 21: Jobs & CronJobs

> 🔗 Related: [Pods](#chapter-5-pods--complete) | [ConfigMaps & Secrets](#chapter-13-configmaps--secrets)

### 🇮🇳 Hindi Explanation
**Job** — Ek kaam karo aur band ho jao (batch processing, DB migration, report generate karna)
**CronJob** — Schedule pe Job chalao (backup, cleanup, daily reports)

```yaml
# ============================================
# JOB — Complete Example
# ============================================
apiVersion: batch/v1
kind: Job
metadata:
  name: db-migration
  namespace: production
spec:
  completions: 1                      # Kitni baar successfully complete karna hai
  parallelism: 1                      # Ek saath kitne pods
  backoffLimit: 4                     # Fail hone pe kitni baar retry
  activeDeadlineSeconds: 600          # Max 10 minutes
  ttlSecondsAfterFinished: 3600       # Complete hone ke 1 hour baad delete

  template:
    metadata:
      labels:
        job: db-migration
    spec:
      restartPolicy: OnFailure        # Never | OnFailure (Job ke liye required)
      containers:
      - name: migration
        image: myapp:2.0
        command: ["npm", "run", "db:migrate"]
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: app-secrets
              key: database_url
        resources:
          requests:
            cpu: 100m
            memory: 256Mi

---
# Parallel Job (batch processing)
apiVersion: batch/v1
kind: Job
metadata:
  name: batch-processor
spec:
  completions: 10                     # Total 10 items process karo
  parallelism: 3                      # 3 pods ek saath
  completionMode: Indexed             # NonIndexed | Indexed
  template:
    spec:
      restartPolicy: OnFailure
      containers:
      - name: processor
        image: processor:1.0
        env:
        - name: JOB_COMPLETION_INDEX
          valueFrom:
            fieldRef:
              fieldPath: metadata.annotations['batch.kubernetes.io/job-completion-index']

---
# ============================================
# CRONJOB — Complete Example
# ============================================
apiVersion: batch/v1
kind: CronJob
metadata:
  name: daily-backup
  namespace: production
spec:
  schedule: "0 2 * * *"              # Har roz 2:00 AM
  # "*/5 * * * *"    → Har 5 min
  # "0 */6 * * *"    → Har 6 ghante
  # "0 9 * * 1-5"   → Weekdays 9 AM
  # "0 0 1 * *"     → Pehle din har mahine
  # "0 0 * * 0"     → Har Sunday midnight

  timeZone: "Asia/Kolkata"           # Timezone (K8s 1.27+)
  startingDeadlineSeconds: 300       # 5 min late tak start ok
  concurrencyPolicy: Forbid          # Allow | Forbid | Replace
  successfulJobsHistoryLimit: 3      # Kitni successful jobs rakhni hain
  failedJobsHistoryLimit: 1          # Kitni failed jobs rakhni hain
  suspend: false                     # true = pause karo

  jobTemplate:
    spec:
      backoffLimit: 2
      activeDeadlineSeconds: 1800    # 30 min max
      template:
        spec:
          restartPolicy: OnFailure
          containers:
          - name: backup
            image: backup-tool:latest
            command:
            - /bin/bash
            - -c
            - |
              DATE=$(date +%Y%m%d_%H%M%S)
              pg_dump $DATABASE_URL | gzip > /backup/db_${DATE}.sql.gz
              aws s3 cp /backup/db_${DATE}.sql.gz s3://mybackup/postgres/
              echo "Backup completed: db_${DATE}.sql.gz"
            env:
            - name: DATABASE_URL
              valueFrom:
                secretKeyRef:
                  name: app-secrets
                  key: database_url
            volumeMounts:
            - name: backup-storage
              mountPath: /backup
          volumes:
          - name: backup-storage
            emptyDir:
              sizeLimit: 5Gi
```

```bash
# Job Commands
kubectl get jobs
kubectl get jobs -n production
kubectl describe job db-migration
kubectl logs job/db-migration
kubectl logs -l job-name=db-migration

# Manually trigger CronJob
kubectl create job manual-backup \
  --from=cronjob/daily-backup

# CronJob Commands
kubectl get cronjobs (cj)
kubectl describe cronjob daily-backup
kubectl patch cronjob daily-backup -p '{"spec":{"suspend":true}}'   # Pause
kubectl patch cronjob daily-backup -p '{"spec":{"suspend":false}}'  # Resume
```

---

## Chapter 22: RBAC

> 🔗 Related: [Service Accounts](#chapter-23-service-accounts) | [Namespaces](#chapter-8-namespaces)

### 🇮🇳 Hindi Explanation
**RBAC (Role-Based Access Control)** — kaun kya kar sakta hai cluster mein.

**4 Objects:**
1. **Role** — Namespace mein permissions define karo
2. **ClusterRole** — Poore cluster mein permissions
3. **RoleBinding** — User/SA ko Role se bind karo (namespace)
4. **ClusterRoleBinding** — User/SA ko ClusterRole se bind karo (cluster-wide)

```
User/ServiceAccount + Role = RoleBinding
User/ServiceAccount + ClusterRole = ClusterRoleBinding
```

```yaml
# ============================================
# ROLE (Namespace-scoped)
# ============================================
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer-role
  namespace: production
rules:
- apiGroups: [""]                     # "" = core API group
  resources: ["pods", "pods/log", "pods/exec"]
  verbs: ["get", "list", "watch"]

- apiGroups: ["apps"]
  resources: ["deployments", "replicasets"]
  verbs: ["get", "list", "watch", "update", "patch"]

- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["get", "list", "watch", "create", "update", "patch"]

- apiGroups: [""]
  resources: ["secrets"]
  resourceNames: ["app-config-secret"]  # Specific secret only
  verbs: ["get"]

- apiGroups: ["batch"]
  resources: ["jobs", "cronjobs"]
  verbs: ["get", "list", "watch", "create", "delete"]

---
# ============================================
# CLUSTERROLE (Cluster-wide)
# ============================================
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: monitoring-role
rules:
- apiGroups: [""]
  resources: ["nodes", "pods", "services", "endpoints", "namespaces"]
  verbs: ["get", "list", "watch"]

- apiGroups: ["apps"]
  resources: ["deployments", "daemonsets", "statefulsets", "replicasets"]
  verbs: ["get", "list", "watch"]

- apiGroups: ["metrics.k8s.io"]
  resources: ["nodes", "pods"]
  verbs: ["get", "list"]

- nonResourceURLs: ["/metrics", "/healthz"]
  verbs: ["get"]

---
# ============================================
# ROLEBINDING (Namespace)
# ============================================
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer-binding
  namespace: production
subjects:
- kind: User
  name: john@company.com
  apiGroup: rbac.authorization.k8s.io

- kind: Group
  name: backend-developers
  apiGroup: rbac.authorization.k8s.io

- kind: ServiceAccount
  name: myapp-sa
  namespace: production

roleRef:
  kind: Role
  name: developer-role
  apiGroup: rbac.authorization.k8s.io

---
# ============================================
# CLUSTERROLEBINDING (Cluster-wide)
# ============================================
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: monitoring-binding
subjects:
- kind: ServiceAccount
  name: prometheus-sa
  namespace: monitoring
roleRef:
  kind: ClusterRole
  name: monitoring-role
  apiGroup: rbac.authorization.k8s.io
```

```bash
# ============================================
# RBAC COMMANDS
# ============================================
kubectl get roles -n production
kubectl get clusterroles
kubectl get rolebindings -n production
kubectl get clusterrolebindings
kubectl describe role developer-role -n production
kubectl describe clusterrole monitoring-role

# Permission check karo
kubectl auth can-i create pods
kubectl auth can-i create pods -n production
kubectl auth can-i delete deployments --as=john@company.com -n production
kubectl auth can-i list secrets \
  --as=system:serviceaccount:production:myapp-sa \
  -n production

# All permissions dekho
kubectl auth can-i --list
kubectl auth can-i --list --as=john@company.com -n production

# Quick create
kubectl create role pod-reader \
  --verb=get,list,watch \
  --resource=pods \
  -n production

kubectl create rolebinding pod-reader-binding \
  --role=pod-reader \
  --user=john@company.com \
  -n production

kubectl create clusterrole node-reader \
  --verb=get,list,watch \
  --resource=nodes

kubectl create clusterrolebinding node-reader-binding \
  --clusterrole=node-reader \
  --serviceaccount=monitoring:prometheus-sa
```

---

## Chapter 23: Service Accounts

> 🔗 Related: [RBAC](#chapter-22-rbac) | [Pods](#chapter-5-pods--complete)

### 🇮🇳 Hindi Explanation
**ServiceAccount** pod ki **identity** hai. Pod jab K8s API se baat karta hai (pod list karna, secret read karna) toh SA use karta hai.

```yaml
# ============================================
# SERVICEACCOUNT CREATE
# ============================================
apiVersion: v1
kind: ServiceAccount
metadata:
  name: myapp-sa
  namespace: production
  labels:
    app: myapp
  annotations:
    eks.amazonaws.com/role-arn: "arn:aws:iam::123:role/myapp-role"  # IRSA (AWS)

automountServiceAccountToken: false   # Auto-mount disable karo (security)

# Image pull secret attach karo
imagePullSecrets:
- name: registry-credentials

---
# ============================================
# ROLE + BINDING + SA = Complete RBAC
# ============================================
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: myapp-role
  namespace: production
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["get", "list", "watch"]
- apiGroups: [""]
  resources: ["secrets"]
  resourceNames: ["myapp-secrets"]
  verbs: ["get"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: myapp-binding
  namespace: production
subjects:
- kind: ServiceAccount
  name: myapp-sa
  namespace: production
roleRef:
  kind: Role
  name: myapp-role
  apiGroup: rbac.authorization.k8s.io

---
# ============================================
# POD MEIN SERVICE ACCOUNT USE KARO
# ============================================
apiVersion: v1
kind: Pod
spec:
  serviceAccountName: myapp-sa
  automountServiceAccountToken: true   # Token mount karo (API access ke liye)
  containers:
  - name: app
    image: myapp:2.0
    # Token automatically yahan milega:
    # /var/run/secrets/kubernetes.io/serviceaccount/token
    # /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
    # /var/run/secrets/kubernetes.io/serviceaccount/namespace
```

```bash
# Service Account Commands
kubectl get serviceaccounts (sa)
kubectl get sa -n production
kubectl describe sa myapp-sa -n production

kubectl create serviceaccount myapp-sa -n production

# Token create karo (K8s 1.24+)
kubectl create token myapp-sa -n production
kubectl create token myapp-sa -n production --duration=8h

# Default SA list karo
kubectl get sa --all-namespaces | grep default
```

---

## Chapter 24: Network Policies

> 🔗 Related: [Services](#chapter-11-services) | [Namespaces](#chapter-8-namespaces)

### 🇮🇳 Hindi Explanation
Default mein K8s mein **saare pods ek dusre se communicate kar sakte hain**. **NetworkPolicy** se firewall rules lagao — micro-segmentation.

**Note:** NetworkPolicy ke liye CNI plugin chahiye jo support karta ho (Calico, Cilium, Weave — Flannel supports nahi karta).

```yaml
# ============================================
# DEFAULT DENY ALL (Zero Trust start)
# ============================================
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
  namespace: production
spec:
  podSelector: {}                     # Saare pods
  policyTypes:
  - Ingress
  - Egress
  # Ingress/Egress rules nahi = sab deny

---
# ============================================
# API SERVICE POLICY
# ============================================
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: api-network-policy
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: api                        # Ye policy in pods pe apply

  policyTypes:
  - Ingress
  - Egress

  ingress:
  # Sirf frontend se allow karo
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 3000

  # Monitoring namespace se metrics scraping allow
  - from:
    - namespaceSelector:
        matchLabels:
          name: monitoring
      podSelector:
        matchLabels:
          app: prometheus
    ports:
    - protocol: TCP
      port: 9090

  egress:
  # Database allow
  - to:
    - podSelector:
        matchLabels:
          app: postgres
    ports:
    - protocol: TCP
      port: 5432

  # Redis allow
  - to:
    - podSelector:
        matchLabels:
          app: redis
    ports:
    - protocol: TCP
      port: 6379

  # DNS allow (ZAROORI!)
  - to:
    - namespace

  namespace: production
spec:
  podSelector:
    matchLabels:
      app: api                       # Ye policy api pods pe apply hogi

  policyTypes:
  - Ingress
  - Egress

  ingress:
  # Sirf frontend pods se traffic allow karo
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 3000

  # Specific namespace se bhi allow karo
  - from:
    - namespaceSelector:
        matchLabels:
          environment: production
      podSelector:                   # AND condition (dono match karne chahiye)
        matchLabels:
          app: frontend

  # External IP range se allow karo
  - from:
    - ipBlock:
        cidr: 10.0.0.0/8
        except:
        - 10.0.1.0/24               # Ye subnet exclude karo

  egress:
  # Sirf database ko connect karne do
  - to:
    - podSelector:
        matchLabels:
          app: postgres
    ports:
    - protocol: TCP
      port: 5432

  # Redis access allow karo
  - to:
    - podSelector:
        matchLabels:
          app: redis
    ports:
    - protocol: TCP
      port: 6379

  # DNS allow karo (ZAROORI! Warna name resolution fail hogi)
  - to:
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: kube-system
    ports:
    - protocol: UDP
      port: 53
    - protocol: TCP
      port: 53

  # External APIs allow karo
  - to:
    - ipBlock:
        cidr: 0.0.0.0/0
        except:
        - 10.0.0.0/8               # Internal network block karo
    ports:
    - protocol: TCP
      port: 443

---
# ============================================
# COMPLETE MICRO-SEGMENTATION EXAMPLE
# ============================================

# Frontend — Sirf ingress controller se traffic le, api ko de
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: frontend-policy
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: frontend
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: ingress-nginx
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: api
    ports:
    - port: 3000
  - to:                             # DNS
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: kube-system
    ports:
    - port: 53
      protocol: UDP

---
# Database — Sirf api aur migration jobs se connections accept karo
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: database-policy
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: postgres
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchExpressions:
        - key: app
          operator: In
          values: ["api", "migration"]
    ports:
    - port: 5432
```

```bash
kubectl get networkpolicies (netpol)
kubectl get netpol -n production
kubectl describe netpol api-network-policy -n production

# Test connectivity
kubectl run test-pod --image=busybox --rm -it --restart=Never -- \
  wget -qO- http://api-service:3000/health
```

### 🎯 Interview Answer
**Q: What are Network Policies in Kubernetes and why are they important?**

By default, all pods can communicate with all other pods across namespaces — this is a security risk. NetworkPolicy acts as a firewall, defining ingress and egress rules based on pod/namespace selectors and IP blocks. Start with a **default-deny-all** policy, then explicitly allow required traffic. Important: NetworkPolicy requires a **CNI plugin that supports it** (Calico, Cilium, Weave Net — Flannel does NOT support). Always include DNS egress (port 53 UDP) otherwise name resolution breaks. Use micro-segmentation: frontend → api → database, with each layer only able to communicate with adjacent layers.

---

## Chapter 25: Horizontal Pod Autoscaler (HPA)

> 🔗 Related: [Deployments](#chapter-10-deployments) | [Resource Requests & Limits](#chapter-16-resource-requests--limits) | [VPA](#chapter-26-vertical-pod-autoscaler-vpa)

### 🇮🇳 Hindi Explanation
**HPA** automatically deployment/statefulset ke pods ki **count** badha ya ghata deta hai load ke basis pe.

**Requirements:**
- **Metrics Server** install hona chahiye (CPU/Memory metrics ke liye)
- Pods mein **resource requests** set honi chahiye

```yaml
# ============================================
# HPA — CPU & Memory based
# ============================================
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
  namespace: production

spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp-deployment

  minReplicas: 2
  maxReplicas: 20

  metrics:
  # CPU based scaling
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70       # 70% CPU pe scale out

  # Memory based scaling
  - type: Resource
    resource:
      name: memory
      target:
        type: AverageValue
        averageValue: 400Mi          # 400Mi average pe scale out

  # Custom metric (Pods)
  - type: Pods
    pods:
      metric:
        name: requests_per_second
      target:
        type: AverageValue
        averageValue: "1000"         # 1000 RPS per pod pe scale

  # External metric (SQS queue length, etc.)
  - type: External
    external:
      metric:
        name: queue_messages_visible
        selector:
          matchLabels:
            queue: myapp-queue
      target:
        type: AverageValue
        averageValue: "30"           # 30 messages per pod

  # Object metric (Ingress RPS)
  - type: Object
    object:
      metric:
        name: requests-per-second
      describedObject:
        apiVersion: networking.k8s.io/v1
        kind: Ingress
        name: myapp-ingress
      target:
        type: Value
        value: "10k"

  # ============================================
  # SCALING BEHAVIOR (Fine-grained control)
  # ============================================
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300    # 5 min stabilize karo scale down se pehle
      policies:
      - type: Percent
        value: 10                         # Max 10% pods per period ghata sako
        periodSeconds: 60
      - type: Pods
        value: 2                          # Ya max 2 pods per period
        periodSeconds: 60
      selectPolicy: Min                   # Min = conservative (dono mein se chhota)

    scaleUp:
      stabilizationWindowSeconds: 0      # Immediately scale up
      policies:
      - type: Percent
        value: 100                        # Max 100% pods double karo per period
        periodSeconds: 15
      - type: Pods
        value: 4                          # Ya max 4 pods per period
        periodSeconds: 15
      selectPolicy: Max                   # Max = aggressive (dono mein se bada)
```

```bash
# Metrics Server install karo
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# HPA commands
kubectl autoscale deployment myapp \
  --cpu-percent=70 \
  --min=2 \
  --max=10
kubectl get hpa
kubectl get hpa -n production
kubectl describe hpa myapp-hpa
kubectl get hpa myapp-hpa --watch    # Live scaling dekho

# Current metrics
kubectl top pods -n production
kubectl top nodes

# HPA delete
kubectl delete hpa myapp-hpa
```

### 🎯 Interview Answer
**Q: How does HPA work in Kubernetes?**

HPA (Horizontal Pod Autoscaler) watches metrics (CPU, memory, custom) and scales the number of pod replicas up or down. It queries **Metrics Server** (or custom metrics adapter) every 15 seconds. Scale-up formula: `desiredReplicas = ceil(currentReplicas * (currentMetric / desiredMetric))`. Example: 3 pods at 90% CPU, target 70% → `ceil(3 * 90/70)` = 4 pods. **Scale-down** has a stabilization window (default 5 min) to prevent flapping. Requirements: pods must have resource requests set (HPA calculates utilization against requests). For production: always set both minReplicas (for baseline availability) and maxReplicas (cost control), and configure scale-down behavior to avoid premature downscaling.

---

## Chapter 26: Vertical Pod Autoscaler (VPA)

> 🔗 Related: [HPA](#chapter-25-horizontal-pod-autoscaler-hpa) | [Resource Requests & Limits](#chapter-16-resource-requests--limits)

### 🇮🇳 Hindi Explanation
**VPA** pods ke **CPU/Memory requests aur limits** automatically adjust karta hai. Pod count nahi badhata — resources badhata/ghataata hai.

**HPA vs VPA:**
- **HPA** = More pods (horizontal scaling)
- **VPA** = Bigger pods (vertical scaling)
- **Dono saath use karo** lekin CPU pe nahi (conflict) — sirf memory pe VPA + CPU pe HPA

```yaml
# VPA install karo
# git clone https://github.com/kubernetes/autoscaler.git
# ./autoscaler/vertical-pod-autoscaler/hack/vpa-up.sh

apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: myapp-vpa
  namespace: production

spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp-deployment

  updatePolicy:
    updateMode: "Auto"               # Off | Initial | Recreate | Auto
    # Off: Sirf recommendations, changes nahi
    # Initial: Sirf naye pods pe apply
    # Recreate: Pods recreate karke apply
    # Auto: Best effort (currently = Recreate)

  resourcePolicy:
    containerPolicies:
    - containerName: myapp
      minAllowed:
        cpu: 50m
        memory: 64Mi
      maxAllowed:
        cpu: "4"
        memory: 4Gi
      controlledResources: ["cpu", "memory"]
      controlledValues: RequestsAndLimits  # RequestsOnly | RequestsAndLimits
```

```bash
kubectl get vpa -n production
kubectl describe vpa myapp-vpa -n production
# Recommendations section mein dekho:
# Lower Bound, Upper Bound, Target (recommended)

# VPA recommendation check karo (Off mode)
kubectl get vpa myapp-vpa -o yaml | grep -A 20 recommendation
```

---

## Chapter 27: Cluster Autoscaler

> 🔗 Related: [HPA](#chapter-25-horizontal-pod-autoscaler-hpa) | [Nodes](#chapter-2-kubernetes-architecture)

### 🇮🇳 Hindi Explanation
**Cluster Autoscaler** **nodes** automatically add ya remove karta hai:
- **Scale Up:** Pods pending hain (resources nahi hain) → Naya node add karo
- **Scale Down:** Node underutilized hai (10 min se) → Node remove karo, pods migrate karo

```yaml
# AWS EKS pe Cluster Autoscaler
# 1. IAM policy create karo
# 2. Service account annotate karo

apiVersion: apps/v1
kind: Deployment
metadata:
  name: cluster-autoscaler
  namespace: kube-system
spec:
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
      annotations:
        cluster-autoscaler.kubernetes.io/safe-to-evict: "false"
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: registry.k8s.io/autoscaling/cluster-autoscaler:v1.29.0
        name: cluster-autoscaler
        command:
        - ./cluster-autoscaler
        - --v=4
        - --stderrthreshold=info
        - --cloud-provider=aws
        - --skip-nodes-with-local-storage=false
        - --expander=least-waste       # least-waste | random | most-pods | price
        - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/my-cluster
        - --balance-similar-node-groups
        - --scale-down-enabled=true
        - --scale-down-delay-after-add=10m
        - --scale-down-unneeded-time=10m
        - --scale-down-utilization-threshold=0.5
        - --max-node-provision-time=15m
```

```bash
# Cluster Autoscaler logs
kubectl logs -l app=cluster-autoscaler -n kube-system -f

# Annotations for safe eviction
kubectl annotate pod mypod \
  cluster-autoscaler.kubernetes.io/safe-to-evict="true"

# Node group status
kubectl describe cm cluster-autoscaler-status -n kube-system
```

---

## Chapter 28: Pod Disruption Budget (PDB)

> 🔗 Related: [Deployments](#chapter-10-deployments) | [Cluster Upgrades](#chapter-34-cluster-upgrades--maintenance)

### 🇮🇳 Hindi Explanation
**PDB** ensure karta hai ki **voluntary disruptions** (node drain, rolling updates, cluster upgrades) ke dauran minimum available pods maintain ho. Involuntary disruptions (node crash) pe apply nahi hota.

**Voluntary disruptions:** Node drain, rolling update, cluster upgrade
**Involuntary disruptions:** Node hardware failure, OOMKill — PDB control nahi karta

```yaml
# ============================================
# PDB — minAvailable
# ============================================
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: myapp-pdb
  namespace: production

spec:
  # Option 1: Minimum available pods
  minAvailable: 2                    # Hamesha 2 pods available hone chahiye
  # minAvailable: "50%"             # Ya percentage

  # Option 2: Maximum unavailable pods
  # maxUnavailable: 1               # Ek waqt mein max 1 pod unavailable
  # maxUnavailable: "25%"           # Ya percentage

  selector:
    matchLabels:
      app: myapp

---
# Practical example: 3 replicas hain
# PDB: minAvailable: 2
# → Drain ke waqt sirf 1 pod evict hoga at a time
# → Pehle 2 pods available honge tab 3rd evict hoga

# PDB: maxUnavailable: 1
# → Same effect — ek waqt mein max 1 pod down
```

```bash
kubectl get pdb -n production
kubectl get poddisruptionbudgets -n production
kubectl describe pdb myapp-pdb -n production
# Shows: Allowed disruptions, Current pods, Desired healthy

# Test karo
kubectl drain worker-1 --ignore-daemonsets --delete-emptydir-data
# PDB violate hoga toh drain wait karega
```

### 🎯 Interview Answer
**Q: What is a PodDisruptionBudget and why is it important?**

PDB ensures high availability during **voluntary disruptions** by setting minimum available or maximum unavailable pods. Without PDB, during node drain or cluster upgrade, K8s might evict all pods of a deployment simultaneously. With `minAvailable: 2` on a 3-replica deployment, K8s ensures at least 2 pods are always running — it will wait before evicting the 3rd until one of the evicted pods reschedules elsewhere. Critical for: cluster upgrades (rolling node upgrades), node maintenance, Cluster Autoscaler scale-down. Always set PDB for production deployments with 2+ replicas.

---

## Chapter 29: Helm — Package Manager

> 🔗 Related: [Deployments](#chapter-10-deployments) | [ConfigMaps & Secrets](#chapter-13-configmaps--secrets)

### 🇮🇳 Hindi Explanation
**Helm** Kubernetes ka **package manager** hai — `apt`, `yum`, `npm` jaise. Complex applications ek command mein deploy karo.

**Key concepts:**
- **Chart** — Package (templates + default values)
- **Release** — Deployed chart instance (ek chart multiple baar deploy ho sakta hai)
- **Repository** — Charts ka store (Artifact Hub)
- **Values** — Configuration jo templates mein inject hoti hai

```bash
# ============================================
# HELM INSTALLATION
# ============================================
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version

# ============================================
# REPO MANAGEMENT
# ============================================
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo add stable https://charts.helm.sh/stable
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add cert-manager https://charts.jetstack.io
helm repo add grafana https://grafana.github.io/helm-charts

helm repo list
helm repo update                     # All repos update karo
helm repo remove stable              # Repo remove karo

# ============================================
# SEARCH
# ============================================
helm search repo nginx               # Installed repos mein search
helm search repo nginx --versions    # All versions
helm search hub prometheus           # Artifact Hub pe

# ============================================
# INSPECT CHART
# ============================================
helm show chart bitnami/postgresql
helm show values bitnami/postgresql  # Default values dekho
helm show all bitnami/postgresql     # Sab info

# ============================================
# INSTALL
# ============================================
# Basic install
helm install my-nginx bitnami/nginx

# Namespace mein install
helm install my-postgres bitnami/postgresql \
  --namespace production \
  --create-namespace

# Values override karo
helm install my-postgres bitnami/postgresql \
  --namespace production \
  --create-namespace \
  --set auth.postgresPassword=MySecurePass123 \
  --set auth.database=myappdb \
  --set primary.persistence.size=50Gi \
  --set primary.resources.requests.cpu=500m \
  --set primary.resources.requests.memory=1Gi

# Values file se override karo (recommended)
helm install my-postgres bitnami/postgresql \
  --namespace production \
  -f postgres-values.yaml

# Multiple values files
helm install my-app ./mychart \
  -f base-values.yaml \
  -f prod-values.yaml             # Later files override earlier ones

# Dry run (kya deploy hoga dekho)
helm install my-app bitnami/nginx \
  --dry-run \
  --debug

# Specific version install
helm install my-nginx bitnami/nginx --version 15.1.0

# Wait for deployment
helm install my-app bitnami/nginx --wait --timeout 5m

# ============================================
# RELEASE MANAGEMENT
# ============================================
helm list                            # All releases
helm list -n production              # Specific namespace
helm list -A                         # All namespaces
helm list --failed                   # Failed releases
helm list --deployed

helm status my-postgres              # Release status
helm history my-postgres             # Revision history
helm get values my-postgres          # Applied values
helm get values my-postgres --all    # All values (including defaults)
helm get manifest my-postgres        # Generated manifests
helm get notes my-postgres           # Release notes

# ============================================
# UPGRADE
# ============================================
helm upgrade my-postgres bitnami/postgresql \
  --namespace production \
  -f postgres-values.yaml

# Install ya upgrade (exists nahi toh install)
helm upgrade --install my-app bitnami/nginx \
  --namespace production \
  --create-namespace \
  -f values.yaml

# Atomic upgrade (fail pe auto rollback)
helm upgrade my-app bitnami/nginx --atomic --timeout 5m

# ============================================
# ROLLBACK
# ============================================
helm rollback my-postgres            # Previous revision
helm rollback my-postgres 3          # Specific revision
helm rollback my-postgres 1 --wait

# ============================================
# UNINSTALL
# ============================================
helm uninstall my-postgres -n production
helm uninstall my-postgres -n production --keep-history  # History rakho

# ============================================
# CUSTOM CHART BANANA
# ============================================
helm create mychart                  # Scaffold create karo

# Chart structure:
# mychart/
#   Chart.yaml           → Chart metadata
#   values.yaml          → Default values
#   charts/              → Dependencies (subcharts)
#   templates/           → K8s manifest templates
#     deployment.yaml
#     service.yaml
#     ingress.yaml
#     configmap.yaml
#     _helpers.tpl        → Template helpers/partials
#     NOTES.txt           → Install ke baad message
#   .helmignore           → Files ignore karo

helm lint mychart                    # Validate karo
helm template mychart                # Templates render karo (dry run)
helm template mychart --debug        # Debug output
helm package mychart                 # .tgz package banao
helm install test-release ./mychart  # Local chart install
```

### Custom Chart Template Example
```yaml
# templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  namespace: {{ .Release.Namespace }}
  labels:
    {{- include "mychart.labels" . | nindent 4 }}
  annotations:
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      {{- include "mychart.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      labels:
        {{- include "mychart.selectorLabels" . | nindent 8 }}
    spec:
      containers:
      - name: {{ .Chart.Name }}
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
        imagePullPolicy: {{ .Values.image.pullPolicy }}
        ports:
        - containerPort: {{ .Values.service.targetPort }}
        {{- if .Values.resources }}
        resources:
          {{- toYaml .Values.resources | nindent 10 }}
        {{- end }}
        {{- if .Values.env }}
        env:
          {{- range .Values.env }}
          - name: {{ .name }}
            value: {{ .value | quote }}
          {{- end }}
        {{- end }}
```

```yaml
# values.yaml
replicaCount: 3

image:
  repository: myapp
  tag: "latest"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 80
  targetPort: 3000

resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 500m
    memory: 512Mi

env:
  - name: NODE_ENV
    value: production

ingress:
  enabled: true
  className: nginx
  hosts:
  - host: myapp.example.com
    paths:
    - path: /
      pathType: Prefix
```

---

## Chapter 30: DNS & Service Discovery (CoreDNS)

> 🔗 Related: [Services](#chapter-11-services) | [StatefulSets](#chapter-19-statefulsets) | [Namespaces](#chapter-8-namespaces)

### 🇮🇳 Hindi Explanation
K8s mein har **Service** ka ek **DNS name** automatically ban jaata hai. **CoreDNS** ye kaam karta hai — `kube-system` namespace mein run karta hai.

**DNS Format:**
```
<service-name>.<namespace>.svc.<cluster-domain>
Default cluster domain: cluster.local

Full FQDN: my-service.production.svc.cluster.local
Same namespace: my-service (short form works)
Cross namespace: my-service.production
```

```bash
# CoreDNS pods
kubectl get pods -n kube-system -l k8s-app=kube-dns
kubectl logs -n kube-system -l k8s-app=kube-dns

# CoreDNS ConfigMap
kubectl get cm coredns -n kube-system -o yaml
kubectl edit cm coredns -n kube-system
```

```yaml
# CoreDNS Corefile
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns
  namespace: kube-system
data:
  Corefile: |
    .:53 {
        errors
        health {
           lameduck 5s
        }
        ready
        kubernetes cluster.local in-addr.arpa ip6.arpa {
           pods insecure
           fallthrough in-addr.arpa ip6.arpa
           ttl 30
        }
        prometheus :9153
        forward . /etc/resolv.conf {
           max_concurrent 1000
        }
        cache 30
        loop
        reload
        loadbalance
    }

    # Custom domain stub zone
    db.company.internal:53 {
        forward . 10.0.0.53          # Internal DNS server
    }

    # External service ke liye rewrite
    example.com:53 {
        forward . 8.8.8.8
    }
```

### DNS Records Types
```bash
# ============================================
# SERVICE DNS RECORDS
# ============================================

# ClusterIP Service:
# A record: my-svc.namespace.svc.cluster.local → ClusterIP
# SRV record: _port._proto.my-svc.namespace.svc.cluster.local

# Headless Service:
# A record: my-svc.namespace.svc.cluster.local → Pod IPs (multiple)
# Pod individual DNS: pod-name.my-svc.namespace.svc.cluster.local

# StatefulSet Pod DNS:
# postgres-0.postgres-headless.production.svc.cluster.local
# postgres-1.postgres-headless.production.svc.cluster.local

# ============================================
# POD DNS
# ============================================
# Pod IP ke dashes se:
# 10-244-1-25.production.pod.cluster.local

# ============================================
# DNS DEBUG
# ============================================
# Debug pod run karo
kubectl run dns-test --image=busybox:1.36 --rm -it --restart=Never -- sh

# Inside pod:
nslookup kubernetes                           # Default service
nslookup my-service                           # Same namespace
nslookup my-service.production                # Cross namespace
nslookup my-service.production.svc.cluster.local  # FQDN
nslookup postgres-0.postgres-headless.production  # StatefulSet pod

cat /etc/resolv.conf
# nameserver 10.96.0.10         ← CoreDNS ClusterIP
# search production.svc.cluster.local svc.cluster.local cluster.local
# options ndots:5

# CoreDNS query logging enable karo (debug)
kubectl edit cm coredns -n kube-system
# log plugin add karo:
# .:53 {
#     log             ← Ye add karo
#     errors
#     ...
# }

# ndots explain:
# ndots:5 = agar hostname mein 5 se kam dots hain toh search domains try karo
# "postgres" → postgres.production.svc.cluster.local ✓ (found!)
# "www.google.com" → www.google.com (4 dots → less than 5 → try search domains first)
```

### 🎯 Interview Answer
**Q: How does DNS-based service discovery work in Kubernetes?**

Kubernetes uses **CoreDNS** running as pods in kube-system namespace. When a Service is created, an **A record** is automatically created: `<service>.<namespace>.svc.cluster.local`. Each pod's `/etc/resolv.conf` is configured with: the CoreDNS ClusterIP as nameserver, and search domains (`<namespace>.svc.cluster.local`, `svc.cluster.local`, `cluster.local`). This allows pods to reach services by short name (`my-service`), namespace-qualified name (`my-service.production`), or FQDN. **Headless services** return individual Pod A records instead of ClusterIP — essential for StatefulSets where each pod needs a stable, unique DNS identity (`postgres-0.postgres-headless.production.svc.cluster.local`).

---

## Chapter 31: Monitoring — Prometheus & Grafana

> 🔗 Related: [Resource Requests & Limits](#chapter-16-resource-requests--limits) | [HPA](#chapter-25-horizontal-pod-autoscaler-hpa)

### 🇮🇳 Hindi Explanation
**Prometheus** metrics collect karta hai, **Grafana** visualize karta hai. **kube-prometheus-stack** Helm chart dono ko saath install karta hai — sabse popular monitoring solution.

```bash
# ============================================
# METRICS SERVER (Basic — kubectl top ke liye)
# ============================================
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# minikube pe
minikube addons enable metrics-server

# Verify
kubectl top nodes
kubectl top pods -A

# ============================================
# KUBE-PROMETHEUS-STACK (Full monitoring)
# ============================================
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Default install
helm install kube-prom-stack \
  prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace

# Custom values ke saath
cat > prometheus-values.yaml << 'EOF'
prometheus:
  prometheusSpec:
    retention: 15d
    retentionSize: "10GB"
    resources:
      requests:
        cpu: 500m
        memory: 1Gi
      limits:
        cpu: 2000m
        memory: 4Gi
    storageSpec:
      volumeClaimTemplate:
        spec:
          storageClassName: fast-ssd
          resources:
            requests:
              storage: 50Gi

grafana:
  adminPassword: "MySecureGrafanaPass"
  persistence:
    enabled: true
    size: 10Gi
  ingress:
    enabled: true
    ingressClassName: nginx
    hosts:
    - grafana.company.com

alertmanager:
  alertmanagerSpec:
    storage:
      volumeClaimTemplate:
        spec:
          resources:
            requests:
              storage: 10Gi

nodeExporter:
  enabled: true

kubeStateMetrics:
  enabled: true
EOF

helm upgrade --install kube-prom-stack \
  prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace \
  -f prometheus-values.yaml

# Access
kubectl port-forward svc/kube-prom-stack-grafana 3000:80 -n monitoring
# http://localhost:3000 (admin/MySecureGrafanaPass)

kubectl port-forward svc/kube-prom-stack-kube-prom-prometheus 9090 -n monitoring
# http://localhost:9090

kubectl port-forward svc/kube-prom-stack-kube-prom-alertmanager 9093 -n monitoring
# http://localhost:9093
```

### Custom Application Metrics
```yaml
# ============================================
# SERVICEMONITOR — Custom app metrics scrape
# ============================================
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: myapp-monitor
  namespace: monitoring
  labels:
    release: kube-prom-stack          # Helm release name match karo
spec:
  namespaceSelector:
    matchNames:
    - production
  selector:
    matchLabels:
      app: myapp
  endpoints:
  - port: http                        # Service port naam
    path: /metrics
    interval: 15s
    scrapeTimeout: 10s

---
# ============================================
# PROMETHEUSRULE — Alerting Rules
# ============================================
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: myapp-alerts
  namespace: monitoring
  labels:
    release: kube-prom-stack
spec:
  groups:
  - name: myapp.availability
    interval: 30s
    rules:
    # Pod down alert
    - alert: PodDown
      expr: |
        kube_pod_status_ready{condition="true",namespace="production"} == 0
      for: 5m
      labels:
        severity: critical
        team: backend
      annotations:
        summary: "Pod {{ $labels.pod }} is not ready"
        description: "Pod has been not ready for more than 5 minutes"
        runbook: "https://wiki.company.com/runbooks/pod-down"

    # High error rate
    - alert: HighErrorRate
      expr: |
        rate(http_requests_total{status=~"5..",job="myapp"}[5m]) /
        rate(http_requests_total{job="myapp"}[5m]) > 0.05
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: "High 5xx error rate: {{ humanizePercentage $value }}"

    # High memory usage
    - alert: HighMemoryUsage
      expr: |
        container_memory_working_set_bytes{namespace="production",container!=""}
        / container_spec_memory_limit_bytes{namespace="production",container!=""} > 0.9
      for: 10m
      labels:
        severity: warning
      annotations:
        summary: "{{ $labels.pod }}/{{ $labels.container }} memory > 90%"

    # Pod CrashLoopBackOff
    - alert: PodCrashLooping
      expr: |
        rate(kube_pod_container_status_restarts_total[15m]) * 60 * 15 > 0
      for: 5m
      labels:
        severity: critical
      annotations:
        summary: "Pod {{ $labels.pod }} is crash looping"

  - name: myapp.performance
    rules:
    # High P99 latency
    - alert: HighLatency
      expr: |
        histogram_quantile(0.99,
          rate(http_request_duration_seconds_bucket{job="myapp"}[5m])
        ) > 2
      for: 10m
      labels:
        severity: warning
      annotations:
        summary: "P99 latency > 2s: {{ $value | humanizeDuration }}"
```

```bash
# ============================================
# USEFUL PROMQL QUERIES
# ============================================

# CPU usage per pod (percentage)
sum(rate(container_cpu_usage_seconds_total{
  namespace="production", container!=""}[5m])) by (pod)
/ sum(kube_pod_container_resource_limits{
  namespace="production", resource="cpu"}) by (pod) * 100

# Memory usage per pod
sum(container_memory_working_set_bytes{
  namespace="production", container!=""}) by (pod)

# Pod restart count (last hour)
increase(kube_pod_container_status_restarts_total{
  namespace="production"}[1h])

# HTTP request rate
rate(http_requests_total{job="myapp"}[5m])

# Error rate percentage
rate(http_requests_total{status=~"5..",job="myapp"}[5m]) /
rate(http_requests_total{job="myapp"}[5m]) * 100

# P95 Latency
histogram_quantile(0.95,
  rate(http_request_duration_seconds_bucket{job="myapp"}[5m]))

# Node CPU utilization
100 - (avg by (instance)(
  rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Node memory available
node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100

# Pending pods
kube_pod_status_phase{phase="Pending"} == 1

# PVC usage
kubelet_volume_stats_used_bytes / kubelet_volume_stats_capacity_bytes * 100
```

---

## Chapter 32: Logging — EFK Stack

> 🔗 Related: [DaemonSets](#chapter-20-daemonsets) | [ConfigMaps & Secrets](#chapter-13-configmaps--secrets)

### 🇮🇳 Hindi Explanation
**EFK = Elasticsearch + Fluent Bit + Kibana**
**Flow:** `Container logs → Fluent Bit (DaemonSet) → Elasticsearch → Kibana`

**Alternative:** Loki + Grafana (lightweight, cost-effective)

```bash
# ============================================
# HELM SE EFK INSTALL
# ============================================
helm repo add elastic https://helm.elastic.co
helm repo add fluent https://fluent.github.io/helm-charts
helm repo update

# Elasticsearch
helm install elasticsearch elastic/elasticsearch \
  --namespace logging \
  --create-namespace \
  --set replicas=1 \
  --set minimumMasterNodes=1 \
  --set resources.requests.cpu=100m \
  --set resources.requests.memory=512Mi \
  --set resources.limits.cpu=1000m \
  --set resources.limits.memory=2Gi \
  --set volumeClaimTemplate.resources.requests.storage=30Gi \
  --set esJavaOpts="-Xmx512m -Xms512m"

# Kibana
helm install kibana elastic/kibana \
  --namespace logging \
  --set service.type=ClusterIP \
  --set resources.requests.cpu=100m \
  --set resources.requests.memory=256Mi

# Fluent Bit (Log collector — DaemonSet)
helm install fluent-bit fluent/fluent-bit \
  --namespace logging \
  -f fluent-bit-values.yaml

# Access
kubectl port-forward svc/kibana-kibana 5601:5601 -n logging
# http://localhost:5601
```

```yaml
# fluent-bit-values.yaml
config:
  service: |
    [SERVICE]
        Daemon Off
        Flush 1
        Log_Level info
        Parsers_File parsers.conf
        HTTP_Server On
        HTTP_Listen 0.0.0.0
        HTTP_Port 2020
        Health_Check On

  inputs: |
    [INPUT]
        Name tail
        Path /var/log/containers/*.log
        multiline.parser docker, cri
        Tag kube.*
        Mem_Buf_Limit 50MB
        Skip_Long_Lines On
        Refresh_Interval 10

  filters: |
    [FILTER]
        Name kubernetes
        Match kube.*
        Merge_Log On
        Keep_Log Off
        K8S-Logging.Parser On
        K8S-Logging.Exclude Off
        Annotations Off
        Labels On

    [FILTER]
        Name grep
        Match kube.*
        Exclude log ^$

  outputs: |
    [OUTPUT]
        Name es
        Match kube.*
        Host elasticsearch-master.logging
        Port 9200
        Logstash_Format On
        Logstash_Prefix kubernetes
        Retry_Limit False
        tls Off
        tls.verify Off

tolerations:
- operator: Exists                   # Sab nodes pe chale
resources:
  limits:
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
```

### Loki + Grafana (Modern Alternative)
```bash
# ============================================
# LOKI STACK (Lightweight alternative)
# ============================================
helm repo add grafana https://grafana.github.io/helm-charts

helm install loki grafana/loki-stack \
  --namespace logging \
  --create-namespace \
  --set grafana.enabled=true \
  --set grafana.adminPassword=admin123 \
  --set loki.persistence.enabled=true \
  --set loki.persistence.size=10Gi

# Grafana mein Loki datasource automatically add hota hai
kubectl port-forward svc/loki-grafana 3000:80 -n logging
# Explore → Loki → Log browser
```

---

## Chapter 33: Kubernetes Dashboard

> 🔗 Related: [RBAC](#chapter-22-rbac) | [Service Accounts](#chapter-23-service-accounts)

### 🇮🇳 Hindi Explanation
**Kubernetes Dashboard** ek web UI hai cluster manage karne ke liye. Production mein RBAC ke saath secure karo.

```bash
# ============================================
# DASHBOARD INSTALL
# ============================================
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v3.0.0/aio/deploy/recommended.yaml

# Verify
kubectl get pods -n kubernetes-dashboard

# ============================================
# ACCESS SETUP
# ============================================
# Admin Service Account banao
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
EOF

# Token generate karo
kubectl create token admin-user -n kubernetes-dashboard
# Token copy karo

# Proxy start karo
kubectl proxy
# http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

# ============================================
# MINIKUBE DASHBOARD
# ============================================
minikube dashboard                    # Auto browser open

# ============================================
# PRODUCTION: INGRESS SE EXPOSE KARO
# ============================================
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dashboard-ingress
  namespace: kubernetes-dashboard
  annotations:
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
    nginx.ingress.kubernetes.io/ssl-passthrough: "true"
spec:
  ingressClassName: nginx
  rules:
  - host: dashboard.company.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: kubernetes-dashboard
            port:
              number: 443
```

---

## Chapter 34: Cluster Upgrades & Maintenance

> 🔗 Related: [etcd Backup](#chapter-35-etcd-backup--restore) | [PDB](#chapter-28-pod-disruption-budget-pdb)

### 🇮🇳 Hindi Explanation
**Zero-downtime cluster upgrade** ke liye:
1. etcd backup lo (ZAROORI!)
2. Control plane upgrade karo
3. Worker nodes ek ek karke upgrade karo (drain → upgrade → uncordon)

**Rule:** Ek time pe sirf **1 minor version** upgrade karo (1.27 → 1.28, never 1.27 → 1.29)

```bash
# ============================================
# PRE-UPGRADE CHECKLIST
# ============================================
kubectl version                        # Current version
kubeadm version
kubectl get nodes                      # All nodes Ready hain?
kubectl get pods -A | grep -v Running | grep -v Completed  # Failed pods?
# etcd backup lo (Chapter 35 dekho)
# Release notes padho!

# ============================================
# STEP 1: CONTROL PLANE UPGRADE
# ============================================

# Available versions check karo
apt-cache madison kubeadm | head -5

# kubeadm upgrade karo
apt-mark unhold kubeadm
apt-get update
apt-get install -y kubeadm=1.29.0-00
apt-mark hold kubeadm
kubeadm version                        # Verify

# Upgrade plan dekho
kubeadm upgrade plan
# Shows: current version, available versions, components

# Upgrade apply karo
sudo kubeadm upgrade apply v1.29.0
# Type 'y' when prompted

# kubelet aur kubectl upgrade karo
apt-mark unhold kubelet kubectl
apt-get install -y kubelet=1.29.0-00 kubectl=1.29.0-00
apt-mark hold kubelet kubectl
systemctl daemon-reload
systemctl restart kubelet

# Verify
kubectl get nodes                      # Master node version updated?

# ============================================
# STEP 2: WORKER NODES UPGRADE (One by one)
# ============================================

# Worker-1 upgrade karo

# Master node se: Worker drain karo
kubectl drain worker-1 \
  --ignore-daemonsets \
  --delete-emptydir-data \
  --grace-period=60 \
  --timeout=5m
# Pods dusre nodes pe move ho jaate hain

# Worker-1 pe SSH karo
ssh worker-1

# kubeadm upgrade
apt-mark unhold kubeadm
apt-get update && apt-get install -y kubeadm=1.29.0-00
apt-mark hold kubeadm
sudo kubeadm upgrade node              # Control plane se alag command!

# kubelet + kubectl upgrade
apt-mark unhold kubelet kubectl
apt-get install -y kubelet=1.29.0-00 kubectl=1.29.0-00
apt-mark hold kubelet kubectl
systemctl daemon-reload
systemctl restart kubelet

# Master node se: Worker wapas active karo
kubectl uncordon worker-1
kubectl get nodes                      # worker-1 Ready + new version?

# Verify pods schedule ho rahe hain
kubectl get pods -A -o wide | grep worker-1

# Same process: worker-2, worker-3, ...

# ============================================
# NODE MAINTENANCE
# ============================================

# Maintenance ke liye drain karo
kubectl drain worker-2 \
  --ignore-daemonsets \
  --delete-emptydir-data \
  --grace-period=30
# Maintenance karo...
kubectl uncordon worker-2

# Sirf scheduling rok do (pods nahi jaate)
kubectl cordon worker-3
kubectl get nodes                      # worker-3: Ready,SchedulingDisabled
kubectl uncordon worker-3

# Node labels update karo
kubectl label node worker-1 maintenance-window="sunday-3am"
```

---

## Chapter 35: etcd Backup & Restore

> 🔗 Related: [Kubernetes Architecture](#chapter-2-kubernetes-architecture) | [Cluster Upgrades](#chapter-34-cluster-upgrades--maintenance)

### 🇮🇳 Hindi Explanation
**etcd** mein poora cluster ka state hai — agar ye corrupt ho jaaye ya delete ho jaaye toh poora cluster data chala jaata hai. **Regular backup ZAROORI hai!**

```bash
# ============================================
# ETCD BACKUP
# ============================================

# etcd endpoint aur certificates dhundho
kubectl get pods -n kube-system | grep etcd
kubectl describe pod etcd-master -n kube-system | grep -E "(command|--listen|--cert|--key|--trusted)"

# Ya static pod manifest dekho
cat /etc/kubernetes/manifests/etcd.yaml | grep -E "(cert|key|endpoints)"

# Backup karo
ETCDCTL_API=3 etcdctl snapshot save /backup/etcd-$(date +%Y%m%d_%H%M%S).db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/healthcheck-client.crt \
  --key=/etc/kubernetes/pki/etcd/healthcheck-client.key

# Backup verify karo
ETCDCTL_API=3 etcdctl snapshot status /backup/etcd-20240115_020000.db \
  --write-out=table
# Shows: Hash, Revision, Total Keys, Total Size

# Backup file check karo
ls -lh /backup/

# ============================================
# AUTOMATED BACKUP (CronJob)
# ============================================
apiVersion: batch/v1
kind: CronJob
metadata:
  name: etcd-backup
  namespace: kube-system
spec:
  schedule: "0 */6 * * *"           # Har 6 ghante
  jobTemplate:
    spec:
      template:
        spec:
          hostNetwork: true
          containers:
          - name: etcd-backup
            image: k8s.gcr.io/etcd:3.5.9-0
            command:
            - /bin/sh
            - -c
            - |
              ETCDCTL_API=3 etcdctl snapshot save \
                /backup/etcd-$(date +%Y%m%d_%H%M%S).db \
                --endpoints=https://127.0.0.1:2379 \
                --cacert=/etc/kubernetes/pki/etcd/ca.crt \
                --cert=/etc/kubernetes/pki/etcd/healthcheck-client.crt \
                --key=/etc/kubernetes/pki/etcd/healthcheck-client.key
              # Old backups clean karo (7 din se purani)
              find /backup -name "*.db" -mtime +7 -delete
            volumeMounts:
            - name: etcd-certs
              mountPath: /etc/kubernetes/pki/etcd
              readOnly: true
            - name: backup-storage
              mountPath: /backup
          restartPolicy: OnFailure
          nodeSelector:
            node-role.kubernetes.io/control-plane: ""
          tolerations:
          - key: node-role.kubernetes.io/control-plane
            effect: NoSchedule
          volumes:
          - name: etcd-certs
            hostPath:
              path: /etc/kubernetes/pki/etcd
          - name: backup-storage
            hostPath:
              path: /var/backups/etcd

# ============================================
# ETCD RESTORE (Emergency!)
# ============================================

# Step 1: API server stop karo (static pod manifest move karo)
mv /etc/kubernetes/manifests/kube-apiserver.yaml /tmp/
# API server automatically stop hoga
sleep 30

# Step 2: etcd stop karo
mv /etc/kubernetes/manifests/etcd.yaml /tmp/
sleep 10

# Step 3: Old data backup karo (safety ke liye)
mv /var/lib/etcd /var/lib/etcd.old.$(date +%Y%m%d)

# Step 4: Snapshot restore karo
ETCDCTL_API=3 etcdctl snapshot restore /backup/etcd-20240115_020000.db \
  --data-dir=/var/lib/etcd \
  --initial-cluster="master=https://192.168.1.10:2380" \
  --initial-cluster-token="etcd-cluster-1" \
  --initial-advertise-peer-urls="https://192.168.1.10:2380" \
  --name="master"

# Step 5: Ownership fix karo
chown -R etcd:etcd /var/lib/etcd

# Step 6: etcd wapas start karo
mv /tmp/etcd.yaml /etc/kubernetes/manifests/
sleep 30
kubectl get pods -n kube-system | grep etcd

# Step 7: API server wapas start karo
mv /tmp/kube-apiserver.yaml /etc/kubernetes/manifests/
sleep 30
kubectl get nodes
kubectl get pods -A
```

### 🎯 Interview Answer
**Q: How do you backup and restore etcd in Kubernetes?**

etcd is the single source of truth for cluster state. **Backup**: Use `etcdctl snapshot save` with etcd certificates (ca.crt, client cert/key). Automate with CronJob every few hours, store backups externally (S3, GCS). Always verify with `etcdctl snapshot status`. **Restore**: Stop API server (move static pod manifest), stop etcd, backup existing data dir, run `etcdctl snapshot restore` to new data dir, restart etcd then API server. In managed clusters (EKS, GKE, AKS), the cloud provider manages etcd backup automatically. Key: test your restore procedure regularly — don't discover it's broken during an outage!

---

## Chapter 36: CRDs & Operators

> 🔗 Related: [Kubernetes Architecture](#chapter-2-kubernetes-architecture) | [RBAC](#chapter-22-rbac)

### 🇮🇳 Hindi Explanation
**CRD (Custom Resource Definition)** — K8s mein apna custom resource type define karo. Jaise `Pod`, `Deployment` built-in resources hain, waise tum `Database`, `KafkaCluster`, `RedisInstance` jaise custom resources bana sakte ho.

**Operator** = CRD + Controller. Complex stateful applications ko K8s-native way se manage karo.

**Popular Operators:**
- PostgreSQL Operator (Zalando, CloudNativePG)
- Kafka Operator (Strimzi)
- Elasticsearch Operator
- Redis Operator
- Prometheus Operator

```yaml
# ============================================
# CRD DEFINITION
# ============================================
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: databases.mycompany.io       # plural.group format

spec:
  group: mycompany.io
  scope: Namespaced                  # Namespaced | Cluster

  names:
    plural: databases
    singular: database
    kind: Database
    shortNames: ["db"]
    categories: ["all"]              # kubectl get all mein dikhe

  versions:
  - name: v1
    served: true
    storage: true                    # Storage version (ek hi ho)
    deprecated: false

    # Schema validation
    schema:
      openAPIV3Schema:
        type: object
        required: ["spec"]
        properties:
          spec:
            type: object
            required: ["engine", "version"]
            properties:
              engine:
                type: string
                enum: ["postgresql", "mysql", "mongodb"]
                description: "Database engine type"
              version:
                type: string
                description: "Database version"
              replicas:
                type: integer
                minimum: 1
                maximum: 10
                default: 1
              storage:
                type: object
                properties:
                  size:
                    type: string
                    pattern: '^[0-9]+Gi$'
                    default: "10Gi"
                  storageClass:
                    type: string
              resources:
                type: object
                properties:
                  cpu:
                    type: string
                    default: "500m"
                  memory:
                    type: string
                    default: "1Gi"
              backup:
                type: object
                properties:
                  enabled:
                    type: boolean
                    default: false
                  schedule:
                    type: string
          status:
            type: object
            properties:
              phase:
                type: string
                enum: ["Pending", "Running", "Degraded", "Failed"]
              replicas:
                type: integer
              readyReplicas:
                type: integer
              connectionString:
                type: string
              conditions:
                type: array
                items:
                  type: object

    # kubectl get databases mein columns
    additionalPrinterColumns:
    - name: Engine
      type: string
      jsonPath: .spec.engine
    - name: Version
      type: string
      jsonPath: .spec.version
    - name: Replicas
      type: integer
      jsonPath: .spec.replicas
    - name: Phase
      type: string
      jsonPath: .status.phase
    - name: Age
      type: date
      jsonPath: .metadata.creationTimestamp

    # Subresources
    subresources:
      status: {}                     # /status endpoint
      scale:                         # kubectl scale support
        specReplicasPath: .spec.replicas
        statusReplicasPath: .status.replicas

---
# ============================================
# CUSTOM RESOURCE INSTANCE
# ============================================
apiVersion: mycompany.io/v1
kind: Database
metadata:
  name: production-postgres
  namespace: production
spec:
  engine: postgresql
  version: "15.4"
  replicas: 3
  storage:
    size: "50Gi"
    storageClass: fast-ssd
  resources:
    cpu: "1000m"
    memory: "2Gi"
  backup:
    enabled: true
    schedule: "0 2 * * *"
```

```bash
# CRD commands
kubectl get crd
kubectl describe crd databases.mycompany.io
kubectl get databases -n production   # Custom resources
kubectl get db -n production          # Short name
kubectl describe db production-postgres -n production
kubectl get all                       # 'all' category mein dikhega
kubectl scale db production-postgres --replicas=5  # Subresource se

# Operator install karo (example: CloudNativePG)
helm repo add cnpg https://cloudnative-pg.github.io/charts
helm install cnpg cnpg/cloudnative-pg \
  --namespace cnpg-system \
  --create-namespace

# CloudNativePG Cluster use karo
cat <<EOF | kubectl apply -f -
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata:
  name: production-db
spec:
  instances: 3
  storage:
    size: 50Gi
  postgresql:
    parameters:
      max_connections: "200"
  backup:
    barmanObjectStore:
      destinationPath: s3://my-backup-bucket/
      s3Credentials:
        accessKeyId:
          name: s3-creds
          key: ACCESS_KEY_ID
        secretAccessKey:
          name: s3-creds
          key: ACCESS_SECRET_KEY
EOF
```

---

## Chapter 37: Pod Security (PSA)

> 🔗 Related: [Pods](#chapter-5-pods--complete) | [Namespaces](#chapter-8-namespaces)

### 🇮🇳 Hindi Explanation
**Pod Security Admission (PSA)** — K8s 1.25 mein stable hua. Pods ke security standards enforce karo namespace level pe.

**3 Security Levels:**
- `privileged` — Koi restriction nahi (system/infra pods ke liye)
- `baseline` — Minimal restrictions (common privilege escalation rokta hai)
- `restricted` — Strict security (best practices enforce karta hai)

**3 Modes:**
- `enforce` — Violating pods reject karo
- `audit` — Log karo lekin allow karo
- `warn` — Warning show karo lekin allow karo

```yaml
# ============================================
# NAMESPACE MEIN PSA LABELS
# ============================================
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    # Strict security enforce karo
    pod-security.kubernetes.io/enforce: restricted
    pod-security.kubernetes.io/enforce-version: latest

    # Audit bhi karo (logs mein jayega)
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/audit-version: latest

    # Warning bhi dikhao
    pod-security.kubernetes.io/warn: restricted
    pod-security.kubernetes.io/warn-version: latest

---
# System namespace — privileged allow karo
apiVersion: v1
kind: Namespace
metadata:
  name: monitoring
  labels:
    pod-security.kubernetes.io/enforce: privileged

---
# ============================================
# RESTRICTED LEVEL KE LIYE POD REQUIREMENTS
# ============================================
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    seccompProfile:
      type: RuntimeDefault            # RuntimeDefault | Localhost

  containers:
  - name: app
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      capabilities:
        drop: ["ALL"]
      # runAsNonRoot: true            # Pod level pe set hai
      # seccompProfile set hai pod level pe
```

```bash
# PSA test karo
kubectl label namespace production \
  pod-security.kubernetes.io/enforce=restricted

# Violation check karo
kubectl run privileged-pod --image=nginx \
  --overrides='{"spec":{"securityContext":{"runAsUser":0}}}' \
  -n production
# Error: violates PodSecurity "restricted:latest"

# Dry run (namespace label add kiye bina test)
kubectl label namespace production \
  pod-security.kubernetes.io/warn=restricted \
  --dry-run=server
```

---

## Chapter 38: Admission Controllers

> 🔗 Related: [CRDs & Operators](#chapter-36-crds--operators) | [Pod Security](#chapter-37-pod-security-psa)

### 🇮🇳 Hindi Explanation
**Admission Controllers** K8s API requests ko process karte hain — resource create/update hone se pehle validate ya modify kar sakte hain.

**2 Types:**
- **Validating** — Request accept ya reject karo (modify nahi karte)
- **Mutating** — Request modify karo (defaults inject karo, labels add karo)

**Built-in Popular Controllers:**
- `NamespaceLifecycle` — Deleted namespace mein objects nahi banne deta
- `LimitRanger` — Resource defaults apply karta hai
- `ResourceQuota` — Quota enforce karta hai
- `PodSecurity` — PSA enforce karta hai
- `MutatingAdmissionWebhook` — Custom webhooks
- `ValidatingAdmissionWebhook` — Custom webhooks

```yaml
# ============================================
# VALIDATING WEBHOOK EXAMPLE
# ============================================
# (Requires a webhook server running)

apiVersion: admissionregistration.k8s.io/v1
kind: ValidatingWebhookConfiguration
metadata:
  name: myapp-validator
webhooks:
- name: validate.myapp.io
  rules:
  - apiGroups: ["apps"]
    apiVersions: ["v1"]
    operations: ["CREATE", "UPDATE"]
    resources: ["deployments"]
    scope: "Namespaced"

  clientConfig:
    service:
      name: webhook-service
      namespace: webhook-system
      path: "/validate-apps-v1-deployment"
    caBundle: <base64-encoded-ca>

  admissionReviewVersions: ["v1"]
  sideEffects: None
  failurePolicy: Fail                # Fail | Ignore
  namespaceSelector:
    matchLabels:
      validate: "true"

---
# ============================================
# MUTATING WEBHOOK EXAMPLE
# ============================================
apiVersion: admissionregistration.k8s.io/v1
kind: MutatingWebhookConfiguration
metadata:
  name: myapp-mutator
webhooks:
- name: mutate.myapp.io
  rules:
  - apiGroups: [""]
    apiVersions: ["v1"]
    operations: ["CREATE"]
    resources: ["pods"]
  clientConfig:
    service:
      name: webhook-service
      namespace: webhook-system
      path: "/mutate-v1-pod"
    caBundle: <base64-encoded-ca>
  admissionReviewVersions: ["v1"]
  sideEffects: None
  # Sidecar inject karne ke liye
  # (Istio, Vault agent, etc. yahi use karte hain)
```

```bash
# Active admission controllers dekho
kubectl get validatingwebhookconfigurations
kubectl get mutatingwebhookconfigurations
kubectl describe validatingwebhookconfiguration myapp-validator
```

---

## Chapter 39: Production Checklist

> 🔗 Related: [Resource Requests](#chapter-16-resource-requests--limits) | [PDB](#chapter-28-pod-disruption-budget-pdb) | [Network Policies](#chapter-24-network-policies)

```yaml
# ============================================
# PRODUCTION-READY DEPLOYMENT TEMPLATE
# ============================================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-service
  namespace: production
  annotations:
    kubernetes.io/change-cause: "v2.1.0 — performance improvements"
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0              # ✅ Zero downtime
  selector:
    matchLabels:
      app: api-service
  template:
    metadata:
      labels:
        app: api-service
        version: "v2.1.0"
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "8080"
    spec:
      serviceAccountName: api-service-sa    # ✅ Dedicated SA
      automountServiceAccountToken: false    # ✅ Token sirf zaroori ho tab

      # ✅ Security Context
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        fsGroup: 2000
        seccompProfile:
          type: RuntimeDefault

      # ✅ Topology spread (HA)
      topologySpreadConstraints:
      - maxSkew: 1
        topologyKey: kubernetes.io/hostname
        whenUnsatisfiable: DoNotSchedule
        labelSelector:
          matchLabels:
            app: api-service
      - maxSkew: 1
        topologyKey: topology.kubernetes.io/zone
        whenUnsatisfiable: DoNotSchedule
        labelSelector:
          matchLabels:
            app: api-service

      containers:
      - name: api
        image: myrepo/api-service:v2.1.0    # ✅ Pinned version (not latest)
        imagePullPolicy: Always

        # ✅ Container security
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true
          capabilities:
            drop: ["ALL"]

        # ✅ Resource limits set hain
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 500m
            memory: 512Mi

        ports:
        - containerPort: 8080
          name: http
        - containerPort: 9090
          name: metrics

        # ✅ ConfigMap & Secret se config
        envFrom:
        - configMapRef:
            name: api-config
        - secretRef:
            name: api-secrets

        # ✅ Sab 3 probes set hain
        livenessProbe:
          httpGet:
            path: /health/live
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 20
          failureThreshold: 3
          timeoutSeconds: 5

        readinessProbe:
          httpGet:
            path: /health/ready
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 10
          failureThreshold: 3
          successThreshold: 1

        startupProbe:
          httpGet:
            path: /health/started
            port: 8080
          failureThreshold: 30
          periodSeconds: 10

        # ✅ Graceful shutdown
        lifecycle:
          preStop:
            exec:
              command: ["/bin/sh", "-c", "sleep 20"]

        volumeMounts:
        - name: tmp
          mountPath: /tmp
        - name: app-config
          mountPath: /app/config
          readOnly: true

      # ✅ Graceful termination
      terminationGracePeriodSeconds: 60

      volumes:
      - name: tmp
        emptyDir:
          sizeLimit: 100Mi
      - name: app-config
        configMap:
          name: api-config

      imagePullSecrets:
      - name: registry-credentials

---
# ✅ Pod Disruption Budget
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: api-service-pdb
  namespace: production
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: api-service

---
# ✅ HPA
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-service-hpa
  namespace: production
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api-service
  minReplicas: 3
  maxReplicas: 20
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
```

### Production Checklist Table
```
✅ WORKLOAD
  □ Resource requests AND limits set hain
  □ Liveness + Readiness + Startup probes configured
  □ Pinned image tags (not :latest)
  □ Non-root user
  □ ReadOnlyRootFilesystem: true
  □ capabilities: drop ALL
  □ allowPrivilegeEscalation: false
  □ preStop hook (graceful drain)
  □ terminationGracePeriodSeconds set
  □ topologySpreadConstraints (HA)
  □ PodDisruptionBudget configured

✅ SCALING
  □ HPA configured (minReplicas >= 2 for production)
  □ Resource requests set (HPA ke liye zaroori)
  □ VPA recommendations review kiye

✅ NETWORKING
  □ NetworkPolicy: default-deny + explicit allows
  □ Service type appropriate (ClusterIP for internal)
  □ Ingress TLS configured
  □ DNS working (nslookup test karo)

✅ STORAGE
  □ StorageClass configured
  □ PVC backup strategy
  □ StatefulSet volumeClaimTemplates

✅ CONFIGURATION
  □ ConfigMaps for non-sensitive config
  □ Secrets for sensitive data
  □ External secret management (Vault/ESO)
  □ No hardcoded credentials in images

✅ OBSERVABILITY
  □ Prometheus metrics exposed (/metrics)
  □ ServiceMonitor configured
  □ PrometheusRule alerts configured
  □ Logs structured (JSON format)
  □ Fluent Bit DaemonSet running
  □ Distributed tracing configured

✅ SECURITY
  □ RBAC: Least privilege principle
  □ Dedicated ServiceAccount per app
  □ automountServiceAccountToken: false (when not needed)
  □ NetworkPolicy micro-segmentation
  □ Image vulnerability scanning in CI/CD
  □ Pod Security Standards (namespace labels)

✅ RELIABILITY
  □ etcd backup automated (CronJob)
  □ Cluster upgrade process tested
  □ Disaster recovery runbook written
  □ Load testing done
  □ Chaos engineering (optional)
```

---

## Chapter 40: Kubernetes Interview Q&A

> 🔗 Related: All chapters

### Top Interview Questions

---

**Q1: What happens when you run `kubectl apply -f deployment.yaml`?**

> 1. **kubectl** YAML parse karta hai aur API server ko REST request bhejta hai
> 2. **kube-apiserver** authentication (who are you?) aur authorization (can you do this?) karta hai
> 3. **Admission Controllers** request validate/mutate karte hain (LimitRanger defaults, PSA checks)
> 4. Object **etcd** mein persist hota hai (desired state store)
> 5. **Deployment Controller** (controller-manager mein) change detect karta hai → **ReplicaSet** create karta hai
> 6. **ReplicaSet Controller** required Pods create karta hai
> 7. **kube-scheduler** har Pod ke liye suitable node find karta hai (resources, affinity, taints dekh ke) → Pod ko node assign karta hai
> 8. **kubelet** (assigned node pe) PodSpec dekh ke **containerd** se container create karwata hai
> 9. **kube-proxy** Service ke liye iptables rules update karta hai
> 10. Container running → readinessProbe pass → Service endpoints mein add

---

**Q2: Liveness vs Readiness vs Startup Probe?**

| Probe | Purpose | Failure Action | Use Case |
|-------|---------|----------------|----------|
| **Liveness** | Container alive hai? | Container restart | Deadlock detection |
| **Readiness** | Traffic ready? | Service se remove | Startup warmup, DB connections |
| **Startup** | Slow start? | Blocks liveness/readiness | Legacy apps, slow JVM startup |

---

**Q3: Why use Deployment over bare Pods?**

> Bare Pods self-heal nahi karte — node die ho toh pod gone. Deployment ek **ReplicaSet** manage karta hai jo desired replica count maintain karta hai. Extra features: rolling updates (zero downtime), rollback (previous RS se), scaling, pause/resume updates.

---

**Q4: What is etcd and why is it critical?**

> etcd ek **distributed, consistent key-value store** hai jo K8s ka **single source of truth** hai — har object (pods, services, secrets, configs) yahan stored hai. kube-apiserver iske saath directly baat karta hai. If etcd goes down → cluster state lost → existing workloads run karte rahte hain lekin koi naya operation nahi ho sakta. **Raft consensus algorithm** use karta hai consistency ke liye. Production mein 3 ya 5 etcd nodes (odd number) HA ke liye.

---

**Q5: How does K8s achieve zero-downtime deployments?**

> Rolling Update strategy ke saath: `maxUnavailable: 0` + `maxSurge: 1` set karo. K8s ek naya pod start karta hai → readinessProbe pass hone ka wait karta hai → ek purana pod terminate karta hai → repeat. Readiness probe ensure karta hai ki naya pod traffic serve karne ready hai before old pod remove hota hai. preStop hook + terminationGracePeriodSeconds graceful shutdown ensure karta hai.

---

**Q6: Deployment vs StatefulSet vs DaemonSet?**

| Object | Use Case | Pod Identity | Storage |
|--------|---------|-------------|---------|
| **Deployment** | Stateless apps (API, Web) | Random | Shared |
| **StatefulSet** | Stateful apps (DB, Kafka) | Stable (pod-0, pod-1) | Per-pod PVC |
| **DaemonSet** | Node-level agents | One per node | HostPath |

---

**Q7: How does Service Discovery work in K8s?**

> **CoreDNS** (kube-system namespace) automatically DNS records create karta hai. Service create hone pe: `<service>.<namespace>.svc.cluster.local` A record ban jaata hai. Pod ka `/etc/resolv.conf` CoreDNS ClusterIP + search domains point karta hai. Short name (`my-svc`) work karta hai same namespace mein, cross-namespace ke liye `my-svc.other-ns` use karo.

---

**Q8: What is the difference between ConfigMap and Secret?**

> **ConfigMap** non-sensitive config (plain text in etcd). **Secret** sensitive data (base64-encoded, NOT encrypted by default). Production mein: etcd encryption at rest enable karo, RBAC se Secret access restrict karo, External Secrets Operator (ESO) se AWS Secrets Manager/Vault se fetch karo. Secret ko volume mount se use karo (automatic rotation support), env var se better.

---

**Q9: How does HPA work?**

> HPA Metrics Server se metrics query karta hai har 15 seconds. Formula: `desiredReplicas = ceil(currentReplicas * currentMetric/desiredMetric)`. Example: 3 pods, 90% CPU, target 70% → `ceil(3 * 90/70)` = 4 pods. Scale-down mein 5 min stabilization window hai (flapping prevent karne ke liye). Requirements: pods mein resource requests set hone chahiye.

---

**Q10: What is RBAC and what are its components?**

> RBAC (Role-Based Access Control): **Role** (namespace permissions), **ClusterRole** (cluster-wide), **RoleBinding** (Role + subjects in namespace), **ClusterRoleBinding** (ClusterRole + subjects globally). Subjects: User, Group, ServiceAccount. Principle: least privilege — sirf zaroori permissions do. Common pattern: Deployment SA ko sirf apne namespace mein pods/configmaps read karne do.

---

**Q11: What is a Service Mesh and when to use it?**

> Service Mesh (Istio, Linkerd) infrastructure layer hai service-to-service communication ke liye. Sidecar proxy (Envoy) har pod mein inject hota hai. Provides: **mTLS** (automatic encryption + auth), **traffic management** (canary, circuit breaking, retries), **observability** (distributed tracing, metrics), **L7 load balancing**. Use karo jab: 10+ microservices, zero-trust security required, canary deployments chahiye, distributed tracing needed. Overhead: complexity aur latency badh jaati hai.

---

**Q12: Node fails — what happens to pods?**

> 1. **Node Controller** node ke `node.kubernetes.io/not-ready` taint lagate hai (40 seconds ke baad node unreachable pe)
> 2. `tolerationSeconds` (default 300 seconds = 5 min) ke baad pods evict hone lagte hain
> 3. **Deployment/ReplicaSet Controller** missing pods detect karta hai → naye pods schedule karta hai dusre nodes pe
> 4. **StatefulSet** pods same naam se reschedule hote hain
> 5. **PDB** ensure karta hai ki minimum available pods maintain ho during eviction
> 6. Node wapas aane pe: existing pods chal rahe hain, kube-proxy rules update hote hain

---

## Chapter 41: Quick Reference Cheat Sheet

> 🔗 Related: [kubectl Reference](#chapter-4-kubectl--complete-reference)

```bash
# ============================================
# CLUSTER INFO
# ============================================
kubectl version --short
kubectl cluster-info
kubectl get nodes -o wide
kubectl top nodes
kubectl get cs                         # Component status
kubectl api-resources | grep -v NAME  # All resource types

# ============================================
# NAMESPACE SHORTCUTS
# ============================================
kubectl config set-context --current --namespace=production
alias k=kubectl
alias kn='kubectl -n'

# ============================================
# GET EVERYTHING
# ============================================
kubectl get all -n production -o wide
kubectl get pods -A --sort-by='.status.containerStatuses[0].restartCount'
kubectl get pods -A | grep -v "Running\|Completed"    # Problem pods
kubectl get events -A --sort-by='.lastTimestamp' | tail -30
kubectl get events -A --field-selector type=Warning

# ============================================
# DEBUGGING
# ============================================
# Pod debug
kubectl describe pod <pod> -n <ns>    # Events section dekho!
kubectl logs <pod> --previous          # Crash ke pehle logs
kubectl exec -it <pod> -- bash
kubectl debug pod/<pod> -it --image=busybox --copy-to=debug-pod

# Service connectivity
kubectl run test --image=busybox --rm -it --restart=Never -- \
  wget -qO- http://myservice:80
kubectl get endpoints myservice        # Pod IPs dekho

# DNS test
kubectl run dns --image=busybox --rm -it --restart=Never -- \
  nslookup myservice.production.svc.cluster.local

# Resource issues
kubectl describe node worker-1 | grep -A 10 "Allocated"
kubectl top pods -A --sort-by=memory
kubectl top pods -A --sort-by=cpu

# ============================================
# QUICK OPERATIONS
# ============================================
# Rolling restart (zero downtime)
kubectl rollout restart deployment/myapp -n production

# Scale
kubectl scale deployment myapp --replicas=0 -n production  # Shutdown
kubectl scale deployment myapp --replicas=3 -n production  # Startup

# Update image
kubectl set image deployment/myapp myapp=myapp:v2.0 -n production
kubectl rollout status deployment/myapp -n production
kubectl rollout undo deployment/myapp -n production        # Rollback

# Port forward (quick access)
kubectl port-forward svc/myapp 8080:80 -n production &

# ============================================
# COPY FILES
# ============================================
kubectl cp pod-name:/app/logs/ ./logs/ -n production
kubectl cp ./local.conf pod-name:/app/config/ -n production

# ============================================
# YAML GENERATION (Dry run)
# ============================================
kubectl create deployment myapp --image=nginx --dry-run=client -o yaml
kubectl create service clusterip myapp --tcp=80:80 --dry-run=client -o yaml
kubectl create configmap myconfig --from-literal=k=v --dry-run=client -o yaml

# ============================================
# FORCE DELETE (Stuck pods)
# ============================================
kubectl delete pod <pod> --grace-period=0 --force -n production

# ============================================
# PRODUCTION HEALTH CHECK
# ============================================
# Failing pods
kubectl get pods -A | grep -v "Running\|Completed\|Succeeded"

# High restarts
kubectl get pods -A -o json | \
  jq '.items[] | select(.status.containerStatuses != null) |
  .metadata.name + " " +
  (.status.containerStatuses[0].restartCount | tostring)' | \
  sort -t'"' -k4 -rn | head -10

# Resource pressure
kubectl get nodes -o json | \
  jq '.items[] | .metadata.name + " " + (.status.conditions[] |
  select(.type == "MemoryPressure" or .type == "DiskPressure") |
  .type + "=" + .status)'

# etcd health
ETCDCTL_API=3 etcdctl endpoint health \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/healthcheck-client.crt \
  --key=/etc/kubernetes/pki/etcd/healthcheck-client.key

# Certificate expiry
kubeadm certs check-expiration

# ============================================
# COMPLETE TOPICS COVERED
# ============================================
```

| # | Chapter | Status |
|---|---------|--------|
| 1 | Kubernetes Overview | ✅ |
| 2 | Architecture (Control Plane + Worker) | ✅ |
| 3 | Installation (Minikube, Kind, kubeadm) | ✅ |
| 4 | kubectl Complete Reference | ✅ |
| 5 | Pods — Complete + All Probes | ✅ |
| 6 | Init Containers & Sidecar Pattern | ✅ |
| 7 | Labels, Selectors & Annotations | ✅ |
| 8 | Namespaces | ✅ |
| 9 | ReplicaSets | ✅ |
| 10 | Deployments + Rolling Updates | ✅ |
| 11 | Services — All 5 Types | ✅ |
| 12 | Ingress & IngressClass + Cert-Manager | ✅ |
| 13 | ConfigMaps & Secrets | ✅ |
| 14 | Taints & Tolerations | ✅ |
| 15 | Node Affinity & Pod Affinity | ✅ |
| 16 | Resource Requests & Limits + QoS | ✅ |
| 17 | ResourceQuota & LimitRange | ✅ |
| 18 | PV, PVC, StorageClass | ✅ |
| 19 | StatefulSets | ✅ |
| 20 | DaemonSets | ✅ |
| 21 | Jobs & CronJobs | ✅ |
| 22 | RBAC | ✅ |
| 23 | Service Accounts | ✅ |
| 24 | Network Policies | ✅ |
| 25 | HPA | ✅ |
| 26 | VPA | ✅ |
| 27 | Cluster Autoscaler | ✅ |
| 28 | Pod Disruption Budget | ✅ |
| 29 | Helm Package Manager | ✅ |
| 30 | DNS & Service Discovery (CoreDNS) | ✅ |
| 31 | Monitoring — Prometheus & Grafana | ✅ |
| 32 | Logging — EFK + Loki Stack | ✅ |
| 33 | Kubernetes Dashboard | ✅ |
| 34 | Cluster Upgrades & Maintenance | ✅ |
| 35 | etcd Backup & Restore | ✅ |
| 36 | CRDs & Operators | ✅ |
| 37 | Pod Security (PSA) | ✅ |
| 38 | Admission Controllers | ✅ |
| 39 | Production Checklist | ✅ |
| 40 | Interview Q&A (12 Questions) | ✅ |
| 41 | Quick Reference Cheat Sheet | ✅ |

---

> 🏠 [Back to Index](./README.md) | 🐳 [Docker Notes](./Docker-Complete-Notes.md)
>
> 📺 **Reference:** [Train with Subham K8s](https://youtu.be/W04brGNgxN4)
> 📖 **TutorialsPoint:** https://www.tutorialspoint.com/kubernetes/index.htm
