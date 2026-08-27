# Registry API

The Phase 3 registry. All routes are relative to `REGISTRY_BIND_ADDR`. Write routes require
`Authorization: Bearer <REGISTRY_WRITE_TOKEN>`.

| Method &amp; path | Auth | Purpose |
|---|---|---|
| `POST /manifests` | write | Publish a manifest + bundle (multipart). Verifies signature, bundle hash, and guardrail scan before storing. |
| `GET /manifests` | — | List catalogued manifests (name, version, publisher, verdict, published-at). |
| `GET /manifests/:id` | — | The full signed manifest JSON, byte-identical to what was published. |
| `GET /manifests/:id/bundle` | — | The stored bundle tarball. Its sha256 equals the manifest's signed `bundle.sha256`. |
| `POST /manifests/:id/activations` | write | Append one ledger record `{activator_pubkey, status}`. |
| `GET /publishers/:pubkey/ledger` | — | Per-manifest `activation_count` for a publisher. |

## `POST /manifests`

`multipart/form-data`, two parts:

- `manifest` — the signed manifest JSON (text field)
- `bundle` — the bundle tarball (file field)

Sending the manifest as a JSON body instead of multipart returns
`400 Invalid boundary for multipart/form-data` — a common first mistake.

### Responses

| Status | When |
|---|---|
| `201` | Stored. Body: `{manifest_id, guardrail_verdict}`. |
| `400` | Signature invalid or expired; **or** uploaded bundle bytes do not match the signed `sha256`. |
| `401` | Missing or wrong write token. |

<div class="measured" markdown>
<span class="prov m">measured</span> Every row above was exercised against a local registry on
2026-08-27: publish → list → get → bundle (hash matched) → activation → ledger, plus `401` for a
missing token and `400` for a tampered bundle.
</div>
