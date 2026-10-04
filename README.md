# Homelab reproducibility

This public repository contains reusable automation only. Real inventory,
credentials, SSH trust state, service endpoints, and environment-specific
validation evidence live outside Git.

## Public/private boundary

Safe to publish here:

- Ansible roles and playbooks;
- generic inventory and configuration examples;
- guarded lifecycle scripts; and
- documentation that contains no operational identifiers.

Keep locally under `$HOME/.local/state/homelab`:

- the real inventory and Proxmox guest definition;
- SOPS policy and encrypted operational secrets;
- the age identity and dedicated SSH identity;
- pinned SSH host keys; and
- detailed validation and recovery evidence.

The default local layout is:

```text
~/.local/state/homelab/
├── age-identity.txt
├── known_hosts
└── private/
    ├── controller.env
    ├── inventory/hosts.yml
    ├── proxmox/test-guest.env
    ├── secrets/grafana-cloud.sops.yml
    ├── sops.yaml
    └── VALIDATION.md
```

Override these locations with `HOMELAB_STATE_DIR`, `HOMELAB_PRIVATE_DIR`, or
the more specific variables documented in `scripts/_controller-env`.

## Initial local setup

Create private directories with restrictive permissions, then copy and complete
the public examples outside the repository:

```sh
state_dir=$HOME/.local/state/homelab
private_dir=$state_dir/private

mkdir -p "$private_dir/inventory" "$private_dir/proxmox" "$private_dir/secrets"
chmod 700 "$state_dir" "$private_dir" "$private_dir/inventory" \
  "$private_dir/proxmox" "$private_dir/secrets"

cp inventory/local.example.yml "$private_dir/inventory/hosts.yml"
cp proxmox/test-guest.example.env "$private_dir/proxmox/test-guest.env"
cp .sops.yaml.example "$private_dir/sops.yaml"
cp secrets/grafana-cloud.example.yml \
  "$private_dir/secrets/grafana-cloud.sops.yml"
chmod 600 "$private_dir"/inventory/hosts.yml \
  "$private_dir"/proxmox/test-guest.env \
  "$private_dir"/secrets/grafana-cloud.sops.yml \
  "$private_dir"/sops.yaml
```

Set `HOMELAB_SSH_KEY` in the local `controller.env` when the dedicated key is
not stored at `$HOMELAB_PRIVATE_DIR/ssh/id_ed25519`.

Generate an age identity outside Git, place its public recipient in the local
`sops.yaml`, then encrypt the local secret document:

```sh
SOPS_AGE_KEY_FILE="$state_dir/age-identity.txt" \
  sops --config "$private_dir/sops.yaml" --encrypt --in-place \
  "$private_dir/secrets/grafana-cloud.sops.yml"
```

Back up or document rotation for the age and SSH identities independently.

## Controller commands

All playbook execution uses the external inventory and strict dedicated SSH
settings through the repository wrapper:

```sh
scripts/verify-controller
scripts/ansible-playbook playbooks/bootstrap.yml
scripts/run-site
scripts/verify-idempotence
scripts/ansible-playbook playbooks/verify.yml
```

`scripts/run-site` decrypts the external SOPS document directly into a temporary
Ansible variables file. It does not write plaintext into the repository.

## Phase 3 disposable Docker proof

The Docker-host role is separate from the common baseline and refuses LXC use
unless the private inventory explicitly opts in. It does not add any user to
the root-equivalent `docker` group by default.

Enable `docker_host_enabled` and, only for an approved disposable LXC,
`docker_host_allow_lxc` in the private inventory. Then run:

```sh
scripts/run-phase3
scripts/verify-phase3-idempotence
```

The proof deploys the versioned service under `services/healthcheck`. Its HTTP
port binds only to guest loopback, it has no secrets or persistent data, and
the verification playbook checks its health and container security settings.
The common firewall reload replaces only its own `inet homelab` table so it
does not erase Docker-managed networking tables. Publishing a production
container port still requires a separately reviewed Docker firewall policy;
the disposable proof does not authorize LAN-facing ports.

## Disposable guest lifecycle

The guarded lifecycle helper reads its definition from the private directory:

```sh
scripts/proxmox-test-guest create
scripts/proxmox-test-guest status
scripts/proxmox-test-guest destroy
```

Creation verifies the expected Proxmox hostname, next free guest ID, template,
and absence of the target guest. Destruction requires the recorded hostname,
tag, and description to match before it proceeds.

## Safety boundary

- Playbooks target only the `test_guests` group.
- The committed inventory contains no hosts.
- SSH hardening and firewall activation require separately verified key access.
- Host-key verification remains strict.
- Production adoption requires an explicit target, backup, rollback plan, and
  health checks.

Credential loss and compromise procedures are documented in
[`docs/recovery-and-revocation.md`](docs/recovery-and-revocation.md).

Keep completed environment evidence in the private validation record. The
public [validation checklist](VALIDATION.md) describes the required proof
without exposing operational data.
