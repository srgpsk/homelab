# Controller credential recovery and revocation

Keep the exact authorized-host ledger and operational paths in the private
state directory. Never place private keys, age identities, tokens, decrypted
secret documents, or real inventory in this repository.

## Dedicated SSH identity

If the private key is lost, use the independent console path to create a new
dedicated key, install its public key on each approved host, verify a new login,
and then remove the old public key. Update the private controller configuration
and retain strict host-key verification.

If the key may be compromised, remove its public key from every host in the
private authorization ledger before restoring automation access. Review access
logs where available and do not reuse the old key.

## Age identity

If the age identity is lost without a protected backup, existing SOPS data
encrypted only to that recipient cannot be recovered. Generate a replacement
identity, update the private SOPS policy, reissue or rotate each affected
credential at its provider, and create a newly encrypted secret document.

If the identity may be compromised, add the replacement recipient, re-encrypt
recoverable documents, verify decryption with the replacement identity, remove
the old recipient, and rotate the underlying service credentials.

## Grafana Cloud credentials

Revoke or rotate affected access policies in Grafana Cloud, update the private
SOPS document, re-encrypt it, and validate telemetry without printing values.
Restart managed Alloy services only through the normal reviewed playbook.

## Controller loss

Clone the public repository, recreate the private state layout described in
the README, restore only protected credential material or follow the rotation
steps above, verify pinned host keys independently, and run controller checks
before any convergence playbook.

The default policy is rotation and rebootstrap. Any future credential backup
must use a separately reviewed encrypted destination outside Git and synced
general-purpose folders.
