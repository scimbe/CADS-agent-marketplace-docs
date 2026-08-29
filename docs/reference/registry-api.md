# Registry API

The Phase 3 registry. All routes are relative to `REGISTRY_BIND_ADDR`. Write routes require
`Authorization: Bearer <REGISTRY_WRITE_TOKEN>`.

A hosted instance runs at `https://registry.bunsenbrenner.org` — the same routes answer there,
so `curl https://registry.bunsenbrenner.org/manifests` returns the live catalogue as JSON. It is a
machine API: there is no HTML page (`GET /` is `404`).

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

<span class="prov m">measured</span> Against the hosted `registry.bunsenbrenner.org` on 2026-08-29:
a 7-manifest portfolio was published (`201` each, incl. a signed `demo_prompt` and an ~18 MB
bundle), then refetched via `GET /manifests/:id` byte-identically.
</div>
