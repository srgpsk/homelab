# Secret input schema

This directory contains examples only. Do not store operational secret files in
this repository, even when they are SOPS-encrypted.

The default external document is:

```text
$HOME/.local/state/homelab/private/secrets/grafana-cloud.sops.yml
```

Create it from `grafana-cloud.example.yml`, edit it through SOPS, and keep the
age identity outside Git. `scripts/run-site` reads and decrypts that external
document without creating a plaintext repository file.

Expected keys are:

```yaml
alloy_prometheus_url: https://example.invalid/api/prom/push
alloy_prometheus_username: "..."
alloy_prometheus_password: "..."
alloy_loki_url: ""
alloy_loki_username: ""
alloy_loki_password: ""
```

Loki values may remain empty while journal collection is disabled.
