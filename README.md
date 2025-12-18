# SEP Helm Chart

## Overview ✅

This repository contains helper values files and examples for deploying the Starburst Enterprise Platform (SEP) using Helm.

Files included:

- `sep-prod-setup.yaml` — production-focused values (coordinator/worker resources, expose settings, environment, etc.)
- `sep-prod-catalogs.yaml` — example catalogs (the `faker` catalog is included as an example)
- `registry-access.yaml` — Harbor registry credentials values (used to pull images)

---

## Prerequisites 🔧

- Helm 3.x installed
- A Kubernetes cluster and `kubectl` configured to talk to it
- A Starburst license file (if required)
- Secure storage for credentials (do **not** commit secrets into Git)

---

## Handling Secrets & Registry Credentials 🔐

- `registry-access.yaml` contains registry settings used by the chart. **Do not commit real credentials** into Git.
- Prefer one of these secure options:
  - Use a Kubernetes image pull secret and reference it via values (recommended).
  - Use sealed-secrets / HashiCorp Vault / SOPS with Helm secrets to keep sensitive values encrypted.

- For the Starburst license referenced by `starburstPlatformLicense` in `sep-prod-setup.yaml`:

```bash
kubectl create secret generic starburstdata --from-file </path/to/license/starburstdata.license>
```

---

## Install / Upgrade (examples) 📦

Replace `<release>` and `<namespace>` with your desired names.

### Install or upgrade using the provided values files:

```bash
# install
helm upgrade sep-prod-cluster oci://harbor.starburstdata.net/starburstdata/charts/starburst-enterprise \
    --install \
    --version 478.0.0 \
    --values ./registry-access.yaml \
    --values ./sep-prod-setup.yaml \
    --values ./sep-prod-catalogs.yaml
```

### Uninstall:

```bash
helm uninstall sep-prod-cluster
```

---

## Catalogs

- `sep-prod-catalogs.yaml` demonstrates adding catalogs (the example contains a `faker` connector). Edit this file to add or modify catalogs.

---

## Validation

- To access the UI if exposed via LoadBalancer, get the external IP/port:

```bash
kubectl get svc -n <namespace>
# then open http://<loadbalancerIP>:8080
```

Or port-forward locally:

```bash
kubectl port-forward svc/<service-name> 8080:8080 -n <namespace>
# then open http://localhost:8080
```


