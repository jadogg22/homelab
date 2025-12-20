# Cluster Documentation

This document outlines common operational procedures for the homelab Kubernetes cluster.

## Storage (Longhorn)

The cluster uses Longhorn for persistent storage. A `StorageClass` named `longhorn-replicated` and longhorn is available.

### Creating a Persistent Volume Claim (PVC)

To create a persistent volume, you need to create a `PersistentVolumeClaim` object. The storage provisioner will automatically create a corresponding `PersistentVolume`.

**Example `pvc.yaml`:**

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-app-data
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: longhorn
  resources:
    requests:
      storage: 5Gi # Adjust the size as needed
```

- `storageClassName` must be `longhorn-replicated`.
- `accessModes` can be `ReadWriteOnce` or `ReadWriteMany` depending on your needs and Longhorn's capabilities.
- `resources.requests.storage` defines the size of the volume.

## Networking (Traefik)

The cluster uses Traefik as the Ingress controller to expose services to the outside world.

### Exposing an Application with an Ingress

To expose a service, you need to create an `Ingress` resource. Traefik is the default IngressClass, and it automatically redirects HTTP traffic to HTTPS.

**Example `ingress.yaml`:**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app-ingress
  annotations:
    # Optional: If you have specific middleware or configurations
    # traefik.ingress.kubernetes.io/router.middlewares: default-redirect-https@kubernetescrd
spec:
  rules:
    - host: my-app.jadogg.com # Replace with your actual domain
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-app-service # The name of your application's service
                port:
                  number: 8080 # The port your service is listening on
```

- Replace `my-app.yourdomain.com` with the desired hostname. You will need to configure your DNS to point this hostname to the Traefik LoadBalancer IP (`10.0.1.150`).
- `service.name` should be the name of the Kubernetes `Service` you want to expose.
- `service.port.number` should be the port number that your `Service` is exposing.
- The `websecure` entrypoint (HTTPS) will be used automatically. For TLS to work, you will need a corresponding TLS certificate managed, for example, by `cert-manager`. See `infrastructure/cert-manager/wildcard.yaml` for an example of how wildcard certificates are handled in this cluster.
