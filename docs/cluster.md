# 🧠 Kubernetes Home Lab with RKE2 + Cilium

A bare-metal Kubernetes cluster using **RKE2** with **Cilium CNI**, designed for:

- CKAD certification prep 🧪  
- Home automation projects 🏡  
- Real-world Kubernetes experience ⚙️

---

## 🖥️ Infrastructure

| Node    | Role         | CPU       | RAM  | Storage                | IP              |
|---------|--------------|-----------|------|------------------------|-----------------|
| yellow  | Control      | i7-9700   | 16GB | 256GB NVMe + 2TB HDD   | 192.168.86.11   |
| blue    | Worker       | i5-7500   | 8GB  | 500GB HDD              | 192.168.86.12   |
| pi4     | Optional ARM | Pi 4 4GB  | 4GB  | microSD                | 192.168.86.13   |
| pi2     | Optional ARM | Pi 4 2GB  | 2GB  | microSD                | 192.168.86.14   |

---

## 📦 Phase 1: System Prep (All Nodes)

```bash
sudo apt update && sudo apt upgrade -y
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab

sudo modprobe br_netfilter
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF

sudo sysctl --system
```

---

## 🚀 Phase 2: Install RKE2 (Control Node - `yellow`)

### Install RKE2 Server

```bash
curl -sfL https://get.rke2.io | sudo INSTALL_RKE2_TYPE=server sh -
```

### Configure RKE2

```bash
sudo mkdir -p /etc/rancher/rke2
sudo tee /etc/rancher/rke2/config.yaml <<EOF
token: "supersecrettoken"
tls-san:
  - 192.168.86.11
cni: cilium
EOF
```

### Start RKE2 Server

```bash
sudo systemctl enable rke2-server.service
sudo systemctl start rke2-server.service
```

### Set Up `kubectl`

```bash
mkdir -p ~/.kube
sudo cp /etc/rancher/rke2/rke2.yaml ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config
```

### Test

```bash
kubectl get nodes
```

---

## 🛠 Phase 3: Install RKE2 (Worker Node - `blue`)

### Install RKE2 Agent

```bash
curl -sfL https://get.rke2.io | sudo INSTALL_RKE2_TYPE=agent sh -
```

### Configure

```bash
sudo mkdir -p /etc/rancher/rke2
sudo tee /etc/rancher/rke2/config.yaml <<EOF
server: https://192.168.86.11:9345
token: "supersecrettoken"
EOF
```

### Start Agent

```bash
sudo systemctl enable rke2-agent.service
sudo systemctl start rke2-agent.service
```

### On `yellow`, verify:

```bash
kubectl get nodes
```

---

## 🕸️ Cilium CNI

Cilium is installed automatically via RKE2 when you specify `cni: cilium`.

### Verify:

```bash
kubectl get pods -n kube-system -l k8s-app=cilium
```

### Optional: Install `cilium-cli`

```bash
curl -LO https://github.com/cilium/cilium-cli/releases/latest/download/cilium-linux-amd64.tar.gz
tar -xzf cilium-linux-amd64.tar.gz
sudo mv cilium /usr/local/bin/
```

---

## 🧪 CKAD Practice Setup

Use:
- [`kubectl`](https://kubernetes.io/docs/reference/kubectl/)
- YAML manifests
- Namespaces, ConfigMaps, Volumes, Resource Limits, Probes, etc.

Practice repo:
- https://github.com/dgkanatsios/CKAD-exercises

killer.sh practice exams

---

## 🏡 Home Automation Ideas

| Project         | Stack                  |
|----------------|------------------------|
| Pi-hole        | DNS blocking w/ Helm   |
| Node-RED       | Event-based logic      |
| Home Assistant | Smart home controller  |
| Grafana/Influx | Metrics dashboard      |
| Mosquitto MQTT | Sensor messaging       |

---

## 🧹 Maintenance

- Restart: `systemctl restart rke2-server` or `rke2-agent`
- Logs:
  ```bash
  journalctl -u rke2-server -f
  journalctl -u rke2-agent -f
  ```
- Reset node:
  ```bash
  sudo /usr/local/bin/rke2-uninstall.sh
  ```

---

## 🔧 Optional GitOps via Helm

Drop Helm charts into:

```bash
/etc/rancher/rke2/helm/
```

and they’ll auto-install on startup.

---

## ✅ Final Checklist

| Task                        | Status  |
|-----------------------------|---------|
| OS installed on all nodes   | ✅       |
| Static IPs and hostnames    | ✅       |
| RKE2 server + agent running | ✅       |
| Cilium installed            | ✅       |
| `kubectl get nodes` shows both | ✅    |
| CKAD test deployments work | ✅       |
