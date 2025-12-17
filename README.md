# 🏠 E-Waste Homelab Kubernetes Cluster (Talos Linux)

At work in order to comply with security policies, We were going to need to update our old workstation boxes to something with tpm2 support.
Instead of sending this old hardware to the landfill, I decided to repurpose them into a home lab and work on my linux admin skills.

## My Approach

A declarative, immutable Kubernetes cluster built on **Talos Linux**. This project uses a **GitOps** approach to manage infrastructure and applications, running on bare metal hardware.

## 🏗️ Architecture

- **OS:** [Talos Linux](https://www.talos.dev/) (Secure, immutable, minimal K8s OS) - This treats even the OS as cattle.
- **Networking (Layer 2):** [MetalLB](https://metallb.universe.tf/) (LoadBalancer implementation for bare metal)
- **Ingress Controller:** [Traefik](https://traefik.io/) (Reverse proxy & SSL management)
- **Storage:** [Longhorn](https://longhorn.io/) (Distributed block storage with NVMe/SATA tiering)
- **DNS & AdBlocking:** [Pi-hole](https://pi-hole.net/) (Network-wide ad blocking & Local DNS)
- **GitOps:** [Argo CD](https://argo-cd.readthedocs.io/) (Continuous Delivery)

## 📂 Project Structure

```bash
homelab-k8s/
├── cluster/                  # Talos OS configs
│   ├── controlplane.yaml   # Machine config for Control Plane
│   ├── worker.yaml         # Machine config for Workers
│   └── patches/            # Patches for updates (e.g., node labels)
├── infrastructure/         # Core K8s System Services
│   ├── metallb/            # IP Address Management
│   ├── longhorn/           # Storage Engine
│   ├── traefik/            # Ingress Controller
│   ├── pihole/             # DNS & AdBlocking
│   └── argocd/             # GitOps Controller
└── apps/                   # App of app approach, declare applications here
└── charts/                 # Here is where my local values.yaml files live for their respective helm charts


```

## 🌐 Network Configuration

- **Subnet:** `10.0.1.0/24`
- **Gateway:** `10.0.1.1`
- **LoadBalancer Pool:** `10.0.1.150 - 10.0.1.250`

| Service               | IP Address   | Domain                | Description                     |
| --------------------- | ------------ | --------------------- | ------------------------------- |
| **Traefik (Ingress)** | `10.0.1.150` | `*.jadogg.com`        | Main entry point for web apps   |
| **Pi-hole (DNS)**     | `10.0.1.151` | `10.0.1.151`          | TCP/UDP DNS Server              |
| **Argo CD**           | `10.0.1.150` | `argocd.jadogg.com`   | GitOps Dashboard (via Traefik)  |
| **Longhorn**          | `10.0.1.150` | `longhorn.jadogg.com` | Storage Dashboard (via Traefik) |

## 🚀 Getting Started

### Prerequisites

- Talos `talosctl` installed.
- Kubernetes `kubectl` installed.
- Helm `helm` installed.

### 1. Provision Nodes (Talos)

Apply machine configurations to the nodes (Control Plane & Workers).

```bash
talosctl apply-config --insecure --nodes <Node-IP> --file talos/controlplane.yaml
talosctl apply-config --insecure --nodes <Node-IP> --file talos/worker.yaml

```

_Note: Ensure `node.kubernetes.io/exclude-from-external-load-balancers` label is removed from Control Plane nodes to allow MetalLB speakers to function._

### 2. Install Infrastructure

All infrastructure is managed via **Helm** charts located in `infrastructure/`.

**Install Order:**

1. **MetalLB:** (Networking)
2. **Longhorn:** (Storage)
3. **Traefik:** (Ingress - Depends on MetalLB & Longhorn)
4. **Pi-hole:** (DNS - Depends on MetalLB & Longhorn)
5. **Argo CD:** (GitOps - Depends on Traefik)

Example Installation (Traefik):

```bash
helm upgrade --install traefik traefik/traefik \
  --namespace traefik --create-namespace \
  -f infrastructure/traefik/values.yaml

```

## 🛠️ Usage

### Local DNS (Pi-hole)

To access services by name (`longhorn.jadogg.com`, `argocd.jadogg.com`), configure your client device to use Pi-hole as its DNS server:

- **DNS Server IP:** `10.0.1.151`

### Accessing Dashboards

- **Traefik:** [http://traefik.jadogg.com/dashboard/](https://www.google.com/search?q=http://traefik.10.0.1.150.nip.io/dashboard/)
- **Longhorn:** [http://longhorn.jadogg.com](https://www.google.com/search?q=http://longhorn.jadogg.com)
- **Argo CD:** [https://argocd.jadogg.com](https://www.google.com/search?q=https://argocd.jadogg.com)
- **Pi-hole:** [http://pihole.jadogg.com/admin](https://www.google.com/search?q=http://pihole.jadogg.com)/admin)

## 🔮 Future Roadmap

- [ ] Deploy Media Stack (jellyfin and arr).
- [x] Setup Minecraft Server (UDP Ingress).
- [x] Configure Cert-Manager for valid SSL certificates (LetsEncrypt).

---
