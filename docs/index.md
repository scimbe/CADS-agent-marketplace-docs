# CADS Manifest Marketplace

A ct-agent can activate a **publisher-signed manifest** that deterministically installs,
runs, and verifies a service behind its own tunnel — instead of an operator hand-wiring a
locally-trusted script.

A manifest is a remote-code-execution primitive by design: it tells a ct-agent what to build
and run. So the honest question is never *what is planned* — it is **what actually works, and
how do we know**. This site answers that, and marks every claim:

<span class="prov m">measured</span> run end to end on a macOS host with **no Docker daemon**,
on 2026-08-27, against the marketplace at `d09eecd`. The output was seen and is quoted verbatim.

<span class="prov a">audited</span> run by the maintainer against a real budget-capped local
model. The transcripts and the guard's error strings were checked against source independently.

<span class="prov n">not built</span> deliberately absent — reserved in the schema, refused at
runtime, and documented as such.

---

## Start here

<div class="grid cards" markdown>

- :material-school: **[Tutorials](tutorials/index.md)**

    Learn by doing. Publish your first manifest end to end and watch the guardrail catch a
    hostile one.

- :material-wrench: **[How-to](how-to/index.md)**

    Task recipes: run the registry locally, sign a manifest, read a verdict.

- :material-book-open-variant: **[Reference](reference/index.md)**

    The registry API, the manifest schema, the guardrail rules, the installer kinds.

- :material-lightbulb-on: **[Explanation](explanation/index.md)**

    Why a signature is never enough, how the harness is caged, and why this site marks
    measured versus claimed.

- :material-application-brackets: **[Demos](demos/index.md)**

    What's actually running on top of `ct-agent`, and whether it has docs of its own.

</div>

---

## What works today

| Capability | Status |
|---|---|
| Manifest signing &amp; verification (ed25519) | <span class="prov m">measured</span> |
| Registry: publish → fetch → bundle, byte-identical | <span class="prov m">measured</span> |
| Guardrail scan at publish time (F.1 / F.2 / F.3) | <span class="prov m">measured</span> |
| Publisher authorization (401 / 400) | <span class="prov m">measured</span> |
| Activation ledger | <span class="prov m">measured</span> |
| Admin dashboard (read-only) | <span class="prov m">measured</span> |
| Binary installer runs without Docker | <span class="prov m">measured</span> |
| Workspace test suite, no daemon | <span class="prov m">measured</span> — 100 / 100 |
| Harness: bounded local-LLM bundle edit | <span class="prov a">audited</span> |
| Harness containment (3 adversarial cases) | <span class="prov a">audited</span> |
| Compose bundle **activation** (needs Docker) | <span class="prov n">not run here</span> |
| Kubernetes installer | <span class="prov n">by design</span> — schema only |
| Real payments | <span class="prov n">by design</span> — ledger only |
| Hosted registry / public dashboard | <span class="prov n">not built</span> |

The [Reference](reference/index.md) gives each of these its own dry, complete entry.
