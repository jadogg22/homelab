# 🛠️ Homelab Migration & SSO Master Plan

### Phase 1: The Infrastructure (TrueNAS & Storage) ✅
* [x] **Create NFS Share** on TrueNAS for `/media`.
* [x] **Install NFS Provisioner** in Kubernetes (truenas-nfs-media).

### Phase 2: The Arr Stack (ArgoCD) 🚧
* [x] **Deploy Gluetun + qBittorrent** (VPN Gateway).
* [x] **Deploy Prowlarr** (Indexer Manager).
* [x] **Deploy Sonarr** (TV Series).
* [x] **Deploy Radarr** (Movies).
* [x] **Deploy Lidarr** (Music).
* [x] **Deploy Readarr** (Books).
* [x] **Deploy FlareSolverr** (Cloudflare Bypass).
* [x] **Deploy Bazarr** (Subtitles).
* [x] **Setup Homepage Dashboard** with Media Stack.
* [ ] **Configure FlareSolverr in Prowlarr** (Settings -> Indexers -> Add Proxy).
* [ ] **Configure Bazarr** to connect to Sonarr/Radarr.
* [ ] **Add Unpackerr** (Automatic Extraction).

### Phase 3: Identity & SSO (Authentik)
* [ ] **Deploy Authentik** via ArgoCD.
* [ ] **Create Traefik Middleware** for Authentik.
* [ ] **Reconfigure Ingresses** to use Authentik Middleware.
* [ ] **Disable App Authentication** (move security to SSO).

### Phase 4: Maintenance & Automation
* [ ] **Setup Renovate** to keep container images updated.
* [ ] **Setup Backup strategy** for Longhorn config volumes.
