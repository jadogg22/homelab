# Homelab Kubernetes Configuration

This repository contains the Kubernetes configuration for my homelab environment, managed with a GitOps approach.

## Directory Structure

The repository is organized as follows:

```
.
├── .sops.yaml
├── apps
│   └── whoami.yaml
├── cluster
│   ├── controlplane.yaml
│   ├── cp.yaml
│   ├── cp_patch.yaml
│   ├── talosconfig
│   ├── volume_patch.yaml
│   ├── worker.yaml
│   └── worker-patch.yaml
├── infrastructure
│   ├── argocd
│   │   └── values.yaml
│   ├── cert-manager
│   │   ├── values.yaml
│   │   └── wildcard.yaml
│   ├── cloudflare
│   │   └── values.yaml
│   ├── longhorn
│   │   └── values.yaml
│   ├── metallb-config.yaml
│   ├── pihole
│   │   ├── pihole-dns-lb.yaml
│   │   ├── pihole-redirect-admin.yaml
│   │   └── values.yaml
│   ├── tailscale
│   │   ├── tailscale-subnet-router.yaml
│   │   └── tailscale-values.yaml
│   └── traefik
│       ├── dashboard.yaml
│       └── values.yaml
└── secrets
    ├── cloudflare-api-token-secret.yaml
    ├── pihole-admin-password.yaml
    └── tailscale_secrets.yaml
```

-   `cluster/`: Contains the core Talos Linux configuration for the Kubernetes cluster nodes.
-   `infrastructure/`: Contains the configuration for core infrastructure applications (e.g., `cert-manager`, `traefik`, `metallb`).
-   `apps/`: Contains the configuration for user-facing applications.
-   `secrets/`: Contains SOPS-encrypted secret files.

## Secret Management with SOPS

This repository uses [SOPS](https://github.com/mozilla/sops) to encrypt and decrypt secrets.

### Prerequisites

-   `sops`
-   `gnupg`

### SOPS Configuration

The `.sops.yaml` file at the root of the repository defines the encryption rules. It is configured to use a GPG key to encrypt all `.yaml` files in the `secrets/` directory.

### Editing Secrets

To edit an encrypted secret file, use the following command:

```bash
sops secrets/<secret-file>.yaml
```

This will open the decrypted file in your default editor. When you save and close the editor, SOPS will automatically re-encrypt the file.

### Encrypting a New Secret

To encrypt a new secret file, use the following command:

```bash
sops --encrypt --in-place secrets/<new-secret-file>.yaml
```

### Decrypting a Secret

To decrypt a secret file and view its contents, use the following command:

```bash
sops --decrypt secrets/<secret-file>.yaml
```
