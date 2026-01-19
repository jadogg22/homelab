# 🛠️ Homelab Migration & SSO Master Plan

### Phase 1: The Infrastructure (TrueNAS & Storage)

* [ ] **Create NFS Share** on TrueNAS for `/media`.
* *Critical:* Set "Maproot User" to `root` (or your specific K8s `PUID`) to allow write access.


* [ ] **Install Jellyfin** on TrueNAS (via Apps/Docker).
* Ensure it has access to the `/media` NFS share path.
* Verify it is reachable at `http://YOUR_NAS_IP:8096`.



### Phase 2: The Cluster "Bridge"

* [ ] **Apply NFS PersistentVolume (PV)** in Kubernetes.
* Create the `PersistentVolume` pointing to the TrueNAS IP.
* Create the `PersistentVolumeClaim` (PVC) named `media-pvc`.


* [ ] **Create Jellyfin "Bridge" Service.**
* Apply the `Service` (ClusterIP) on port 8096.
* Apply the `Endpoint` manually mapping the Service to `YOUR_NAS_IP`.
* Apply the `Ingress` pointing to `jellyfin.jadogg.com` (using your existing Cert-Manager/Traefik).



### Phase 3: The Arr Stack (ArgoCD)

* [ ] **Create Parent Helm Chart.**
* Set up the `Chart.yaml` and `values.yaml` structure.
* Configure `apps` list (Sonarr, Radarr, Prowlarr) in `values.yaml`.


* [ ] **Configure Child Apps Persistence.**
* Update Helm values for Sonarr/Radarr to use `existingClaim: media-pvc`.


* [ ] **Deploy via ArgoCD.**
* Sync the parent application and verify all pods start and can see `/media`.



### Phase 4: Identity & SSO (Authentik)

* [ ] **Deploy Authentik** via ArgoCD (using the official Helm chart).
* [ ] **Create Traefik Middleware.**
* Define the `authentik-auth` Middleware in K8s (pointing to the Authentik Outpost).


* [ ] **Reconfigure Ingresses.**
* Add annotation `traefik.ingress.kubernetes.io/router.middlewares: media-authentik-auth@kubernetescrd` to Sonarr, Radarr, and Prowlarr Ingresses.


* [ ] **Disable App Authentication.**
* Log into Sonarr/Radarr manually one last time.
* Go to **Settings > General > Security**.
* Change Authentication to **None**.
* *Test:* Verify that `https://sonarr.jadogg.com` now redirects to your Authentik login page.
