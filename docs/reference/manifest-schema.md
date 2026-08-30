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
| `demo_prompt` | object | **Optional.** Guided natural-language reconfiguration: bounded, signed rules for turning a user's free text into a config change. See [below](#demo_prompt). Backward-compatible — absent on manifests that predate it. |
| `signature` | hex(64) | ed25519 over a domain-separated, length-prefixed preimage of all of the above. |

!!! note "Signature scope"
    The signature covers the bundle **hash**, not the bundle bytes directly. That is why a
    tampered upload is caught: its bytes no longer hash to the signed value.

<div class="measured" markdown>
<span class="prov m">measured</span> A manifest with the core fields above (no `demo_prompt`) was
signed with a throwaway key and round-tripped through the registry byte-identically on 2026-08-27.
</div>

## `demo_prompt`

An optional `DemoPrompt` block declaring how a demo may be reconfigured from free text through an
LLM, **without letting the model widen the rules**. It is covered by the signature (a tampered
options list, or adding/removing the block after signing, invalidates the signature) and is
backward-compatible: when absent, the signed preimage is byte-identical to a manifest that never
had the field.

| Field | Type | Meaning |
|---|---|---|
| `system` | string | The rules handed to the LLM. |
| `parameters[]` | list | What is tunable. A `Vec`, not a map — signing needs a fixed field order. |
| `examples[]` | list | Few-shot prompts. Descriptive only; not part of enforcement. |

Each `parameters[]` entry is `{name, type, …}`, where `type` selects the bounded kind:

| `type` | Extra fields | Accepted value |
|---|---|---|
| `enum` | `options: [string]` | exactly one of `options` |
| `color` | — | a `#rrggbb` hex string |
| `multiselect` | `options: [string]`, `note?` | a subset of `options` |
| `int` | `min`, `max` | an integer in `[min, max]` |

!!! warning "Enforcement is code, not prompt"
    The LLM only *proposes* a value; the consumer must check each proposed value against the
    declared `options` **in code** and reject anything outside them — the same discipline the
    guardrail scanner applies to bundles. The `system` string is guidance; the signed schema is
    the boundary. For dynamic option sets (e.g. which data an API actually returned), the allowed
    set is computed by the consumer's own code from the real response, never proposed by the model.

<div class="measured" markdown>
<span class="prov m">measured</span> A `binary` manifest carrying a `demo_prompt` (enum, color, and
multiselect-with-`note` parameters) was signed and round-tripped through the live registry
byte-identically on 2026-08-29 — the block came back exactly as signed.
</div>

!!! note "Source pending merge"
    The hosted `registry.bunsenbrenner.org` already runs the `manifest-core` change that adds this
    field, but its source lands via `CADS-agent-marketplace` **PR #43** (not yet merged to `main`).
    Reading the code at `main` will not show `demo_prompt` until that merges.
