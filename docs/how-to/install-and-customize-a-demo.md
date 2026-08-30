# Install and customize a demo locally

Every demo on `bunsenbrenner.org` is a signed manifest. You don't need the operator to redeploy
anything to run one yourself — `ct-agent manifest activate` fetches it, verifies the signature and
bundle hash, unpacks it, and brings it up, entirely on your own machine.

## 1. Get the manifest

Every published manifest is listed at the registry:

```bash
curl -s https://registry.bunsenbrenner.org/manifests | python3 -m json.tool
```

Find the demo by `name`, note its `manifest_id`, then fetch the full signed manifest:

```bash
curl -s https://registry.bunsenbrenner.org/manifests/<manifest_id> -o demo.manifest.json
```

`CT_MANIFEST_URL` below can point at this file directly (a local path), or at the registry URL
itself — either works.

## 2. Decide who you trust

`manifest activate` refuses to run anything unless the manifest's `publisher_pubkey` is on an
allowlist **you** supply — a manifest is never trusted just because the registry served it. See
[The trust model](../explanation/the-trust-model.md) for why a signature alone is never enough.

For a demo you've decided to trust, put its `publisher_pubkey` (already in the manifest JSON from
step 1) in a file, one 64-hex key per line:

```bash
python3 -c "import json;print(json.load(open('demo.manifest.json'))['publisher_pubkey'])" \
  > trusted-publishers.txt
```

!!! note "This is trust-on-first-use, not independent verification"
    Reading the pubkey out of the manifest you're about to install is convenient, not
    independent — you're trusting the registry's TLS connection, not a second, out-of-band
    source. For anything beyond casual local trial, get the publisher's pubkey from somewhere
    else first (the demo's own repo README, a signed release, a channel from someone you already
    trust) and compare it before adding it to your allowlist.

## 3. Activate it

```bash
export CT_MANIFEST_URL=demo.manifest.json          # or the registry URL from step 1
export CT_MANIFEST_PROJECT_NAME=my-local-<demo-name>
export CT_MANIFEST_WORK_DIR=$HOME/demos/<demo-name>
export CT_MANIFEST_TRUST_ALLOWLIST_FILE=trusted-publishers.txt
ct-agent manifest activate
```

This prints an install report as JSON and exits non-zero unless `"status": "ok"`. What actually
happens depends on the manifest's `installer_kind` (see [Installer kinds](../reference/installer-kinds.md)):

- **`compose`** — the bundle is unpacked into `CT_MANIFEST_WORK_DIR`, then `docker compose up -d
  --build` runs there. Needs a running Docker daemon.
- **`binary`** — a single hash-verified executable is unpacked and run directly, under a bounded
  process, from the same directory. No Docker daemon needed.

## 4. Customize it

Every manifest ships an `env_template` — the exact list of config/secret values the demo actually
reads, each with a name, whether it's required, and a description (visible in the manifest JSON
from step 1, under `env_template`). Supply your own values in a local file:

```bash
cat > my-demo.env <<'EOF'
LITELLM_BASE_URL=https://your-own-litellm-endpoint/v1
LITELLM_API_KEY=sk-...
EOF
export CT_MANIFEST_ENV_FILE=my-demo.env
ct-agent manifest activate   # re-run with the env file set
```

These values are **yours** — they're written straight to `CT_MANIFEST_WORK_DIR/.env`, never read
from or sent back to the manifest itself. For deeper changes than an env var covers (a `compose`
kind's actual compose file, its mounted config, its source, whatever the bundle contains), edit
the files directly inside `CT_MANIFEST_WORK_DIR` — that directory is now yours; `ct-agent` doesn't
touch it again unless you re-run `activate`.

!!! warning "Re-activating overwrites `.env`, not your other edits"
    Re-running `manifest activate` re-unpacks the bundle and rewrites `CT_MANIFEST_WORK_DIR/.env`
    from `CT_MANIFEST_ENV_FILE`. If you've hand-edited the compose file or other bundle contents
    directly, keep a copy before re-activating, or apply your changes on top afterward.

## Optional: record the activation on the registry

If you want your install recorded in the registry's activation ledger (informational only — it
does not gate anything), set `CT_MANIFEST_REGISTRY_URL`, `CT_MANIFEST_REGISTRY_WRITE_TOKEN`, and
`CT_MANIFEST_ACTIVATOR_PUBKEY` (your own holder pubkey) alongside the variables above. This is
entirely optional — everything in steps 1–4 works without it.

See the [Reference](../reference/index.md) for every `ct-agent manifest` subcommand and env var,
and [Publish your first manifest](../tutorials/publish-your-first-manifest.md) if you're building
a demo of your own rather than installing someone else's.
