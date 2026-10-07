# home-ops


## Overview

This is the monorepository where I keep everything for my home Kubernetes cluster
and the workloads running on it. Every cluster resource is declared as **YAML** and
reconciled automatically.

I try to adhere to Infrastructure as Code (IaC) and GitOps practices using
[Kubernetes](https://kubernetes.io/), [Docker](https://www.docker.com/),
[Argo CD](https://argo-cd.readthedocs.io/), [SOPS](https://github.com/getsops/sops),
[Prometheus](https://prometheus.io/), and
[GitHub Actions](https://github.com/features/actions).

The purpose here is to learn k8s and GitOps while running my real services on it.

---

## ⛵ Kubernetes

My cluster runs **[Talos Linux](https://www.talos.dev/)** on three repurposed
iMacs, with embedded etcd on all three nodes so every node is a control-plane
member. Nodes are named after Japanese fruits.

Persistent volumes use `local-path`; bulk storage and backups live on an external
SSD attached to **ringo**, which also acts as the **data host** for services that
deliberately stay outside the cluster.

All workloads are containerized with **Docker** and pushed to
[GHCR](https://ghcr.io/), then deployed to the cluster as plain **YAML** manifests
managed by Argo CD.

### Core Components

- **GitOps & Delivery**: [Argo CD](https://argo-cd.readthedocs.io/) reconciles the cluster against this repository using a root Application.
- **Networking & Ingress**: [Traefik](https://traefik.io/) for ingress, [cloudflared](https://github.com/cloudflare/cloudflared) for public access.
- **Security & Secrets**: [SOPS](https://github.com/getsops/sops) with [age](https://github.com/FiloSottile/age) encrypts secrets in git.
- **Observability**: [Prometheus](https://prometheus.io/) + [Grafana](https://grafana.com/) for metrics, dashboards, and alerting.
- **Automation & CI/CD**: [GitHub Actions](https://github.com/features/actions) builds and pushes images on every commit.

### Directories

```sh
📁 kubernetes
├── 📁 apps              # app manifests, grouped by namespace
│   ├── 📁 base          # base app configuration
│   └── 📁 overlays      # cluster-specific overlays
├── 📁 clusters          # Argo CD Application definitions (app-of-apps)
├── 📁 bootstrap         # one-time cluster bootstrap
└── 📁 components        # re-usable kustomize components
📁 manifests             # raw YAML
📁 secrets               # SOPS-encrypted manifests
```

---

## 🌐 DNS

Public hostnames resolve through **Cloudflare**, with ingress handled by a
**cloudflared** tunnel, so no ports are open on the router and no origin IP is
exposed.

---

## 🔧 Hardware

### Kubernetes Cluster

| Name   |  Device                    | CPU                          | RAM         | OS Disk | OS    | Purpose                       |
| ------ |   ------------------------- | ---------------------------- | ----------- | ------- | ----- | ----------------------------- |
| ringo  |iMac 27" (iMac17,1, 2015) | Core i7-6700K · 4c/8t        | 32 GB DDR3L | 512 GB  | Talos | control-plane, data host      |
| mikan   | iMac 27" (iMac15,1, 2014) | Core i5-4690 · 4c/4t         | 16 GB DDR3L | 256 GB  | Talos | control-plane, worker         |
| ichigo |  iMac 21.5" (iMac14,1, 2013) | Core i5-4570R · 4c/4t      | 16 GB DDR3L | 256 GB  | Talos | control-plane, worker         |

Total CPU: 12 Cores / 16 Threads
Total RAM: 64 GB

All three nodes are equal members of the etcd quorum. `ringo` carries the extra
RAM and the external SSD because it plays the data-host role.

---

## 🤝 Thanks

Thanks for checking out this repo :D

---
