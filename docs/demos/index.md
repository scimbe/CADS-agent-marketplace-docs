# Demos

What's actually running on top of `ct-agent`, and what's documented about it. Not every demo
has its own docs site — this page says so plainly rather than linking somewhere thin.

## With their own Diátaxis docs site

- **[Sort Arena](https://scimbe.github.io/CADS-DEMO-sort-docs/)** — live multi-agent sorting
  competition (`sort.bunsenbrenner.org`), source at
  [CADS-DEMO-sort](https://github.com/scimbe/CADS-DEMO-sort).
- **[Local LLM demo](https://scimbe.github.io/CADS-DEMO-local-llm-docs/)** — source at
  [CADS-DEMO-local-llm](https://github.com/scimbe/CADS-DEMO-local-llm).
- **[Kali desktop](https://scimbe.github.io/CADS-kali-desktop-docs/)** — full Kali Linux desktop
  streamed to the browser, gated by Keycloak (`kali.bunsenbrenner.org`), source at
  [CADS-kali-desktop](https://github.com/scimbe/CADS-kali-desktop).

## Documented, no dedicated site yet

Real, running services — the documentation lives in the repo's own README rather than a
published site.

- **[Code-Review demo](https://github.com/scimbe/CADS-DEMO-codereview)** — static-tool + LLM
  code review following the Harness Correction methodology; Phase 1 only, no VS Code
  integration yet.

## No public docs

- **game2048** (`game2048.bunsenbrenner.org`) — deployed, no dedicated repo or docs.

---

None of the demos above are published *as* installable manifests on this marketplace yet — see
[what works today](../index.md#what-works-today) for the actual state of the installer/registry
itself. This page is about what exists to *look at and read*, not what you can `manifest
activate` right now.
