# Manifest schema

A `ServiceManifest` (in `crates/manifest-core`). Every field is covered by the ed25519 signature.

| Field | Type | Meaning |
|---|---|---|
| `publisher_pubkey` | hex(32) | The publisher's ed25519 **public** holder key. |
| `manifest_id` | hex(32) | Stable identifier for this manifest. |
| `name` | string | Human name, e.g. `hello-world`. |
| `version` | string | Publisher-assigned version. |
| `installer_kind` | enum | `compose`, `binary`, or `k8s`. See [Installer kinds](installer-kinds.md). |
| `bundle.url` | string | Where the bundle tarball is fetched from. |
| `bundle.sha256` | hex(32) | Hash the fetched/uploaded bundle must match. |
| `bundle.compose_file` | string | Path within the bundle to the compose file (compose kind). |
| `env_template[]` | list | Declared env vars: `{name, required, description}`. |
| `verify.script` | string | Path within the bundle to a verification script. |
| `verify.timeout_secs` | int | Budget for the verify script. |
| `issued_at` | unix secs | Signing time. |
| `expires_at` | unix secs | After this, the registry rejects the manifest as expired. |
| `signature` | hex(64) | ed25519 over a domain-separated, length-prefixed preimage of all of the above. |

!!! note "Signature scope"
    The signature covers the bundle **hash**, not the bundle bytes directly. That is why a
    tampered upload is caught: its bytes no longer hash to the signed value.

<div class="measured" markdown>
<span class="prov m">measured</span> A manifest with exactly these fields was signed with a
throwaway key and round-tripped through the registry byte-identically on 2026-08-27.
</div>
