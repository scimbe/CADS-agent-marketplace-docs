# Sign a manifest

A manifest is ed25519-signed over a domain-separated, length-prefixed preimage. The `dev_sign`
example (in `crates/manifest-core/examples`) mirrors the production CLI field for field, so you
can sign without a full `ct-agent` build:

```bash
export CT_MANIFEST_HOLDER_KEY=<64 hex — your ed25519 private key>
export CT_MANIFEST_NAME=hello-world
export CT_MANIFEST_VERSION=1.0.0
export CT_MANIFEST_KIND=compose                # compose | binary | k8s
export CT_MANIFEST_COMPOSE_FILE=docker-compose.yml
export CT_MANIFEST_BUNDLE_URL=https://example/hello-world.tar.gz
export CT_MANIFEST_BUNDLE_SHA256=<sha256 of the bundle tarball>
export CT_MANIFEST_VERIFY_SCRIPT=verify.sh
export CT_MANIFEST_ENV_VARS="HELLO_TEXT:true:text to echo"   # name:required:description, ; separated
cargo run --quiet --example dev_sign > hello-world.manifest.json
```

The output is the complete signed manifest as JSON. Only the **public** holder key and the
signature are in it; the private key never leaves your shell.

!!! warning "The bundle hash is signed"
    `CT_MANIFEST_BUNDLE_SHA256` is covered by the signature. If the bundle bytes change by even
    one byte, the registry rejects the upload with `400` — see
    [Read a guardrail verdict](read-a-guardrail-verdict.md) and the
    [Registry API](../reference/registry-api.md).

See the [Manifest schema](../reference/manifest-schema.md) for every field's meaning.
