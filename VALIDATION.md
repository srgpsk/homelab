# Validation checklist

Keep environment-specific evidence outside this public repository. A completed
local record should establish all of the following without publishing hostnames,
addresses, fingerprints, account identifiers, or service endpoints.

- Controller dependencies and credential file modes pass.
- SSH host keys were verified through an independent console and pinned.
- The disposable guest was created from recorded private inputs.
- Dedicated-key SSH and passwordless sudo work before hardening.
- Root and password SSH authentication are disabled after verification.
- Firewall, time, packages, and required services match desired state.
- Alloy configuration validates and remote metrics delivery succeeds.
- A second convergence run reports no unintended changes.
- The guest can be destroyed, recreated, and converged to the same state.
- Production systems remain unchanged until separately approved.

For the Phase 3 disposable workload proof, also establish that:

- Docker Engine and Compose are installed by a separate, explicitly enabled
  role without silently replacing an existing container runtime.
- The automation account is not granted root-equivalent Docker socket access.
- The Docker daemon uses bounded local logging and live restore.
- The pinned test image runs as a non-root user with a read-only root
  filesystem, dropped capabilities, and `no-new-privileges`.
- The web health endpoint returns the expected response on guest loopback and
  is not exposed on the guest LAN address.
- Reloading the managed host firewall preserves Docker-managed networking.
- Two consecutive Phase 3 convergence runs report no unintended changes.
