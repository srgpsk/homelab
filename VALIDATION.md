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
