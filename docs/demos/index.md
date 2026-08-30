# Demos

What's actually running on top of `ct-agent`, how you can see it, and what's documented about it.
Every entry says plainly what state it is in rather than linking somewhere thin.

## Installable from the marketplace registry

These are published **as signed, installable manifests** on the hosted registry at
`registry.bunsenbrenner.org` — `curl https://registry.bunsenbrenner.org/manifests` lists them as
JSON. Each ships in **two versions**: a base manifest and a **guided** variant that carries a
signed [`demo_prompt`](../reference/manifest-schema.md#demo_prompt) (bounded natural-language
reconfiguration — location, colours, fonts, which data, voice, language, etc.).

| Demo | What it does | Repo |
|---|---|---|
| **phototools** | Sorts a folder of photos by date + place from EXIF, no ML | [CADS-DEMO-phototools](https://github.com/scimbe/CADS-DEMO-phototools) |
| **newsletter** | Weather-newsletter with charts, rendered to HTML + PDF | [CADS-DEMO-newsletter](https://github.com/scimbe/CADS-DEMO-newsletter) |
| **contractcheck** | Contract/clauses review to a Markdown report | [CADS-DEMO-contractcheck](https://github.com/scimbe/CADS-DEMO-contractcheck) |
| **diagram** | Text → architecture diagram image | [CADS-DEMO-diagram](https://github.com/scimbe/CADS-DEMO-diagram) |
| **temporal-poc** | Event log → timeline | [CADS-DEMO-temporal-poc](https://github.com/scimbe/CADS-DEMO-temporal-poc) |
| **travel** | Live routing over OSRM (compose service) | [CADS-DEMO-travel](https://github.com/scimbe/CADS-DEMO-travel) |
| **podcast** | Script → two-voice German audio (Piper + cloned voice) | [CADS-DEMO-podcast](https://github.com/scimbe/CADS-DEMO-podcast) |

Signatures, bundle hashes and the guardrail scan are verified at publish time; the manifests are
refetchable byte-identically via `GET /manifests/:id`.

## Visitable portfolio (the demo wrapper)

The same demos are also presented as **visitable, application-oriented services in the
bunsenbrenner.org style** — a tool you use, not a marketing page — through a shared wrapper. The
gallery currently carries **eight** demos:

`phototools` · `newsletter` · `contractcheck` · `diagram` · `temporal-poc` · `podcast` ·
`explainer` (narrated video) · `travel` (live service).

A permanent public deployment is pending a dedicated subdomain; until then the wrapper is served
on demand. `explainer` is visitable but is not yet published as a manifest.

## With their own Diátaxis docs site

- **[Sort Arena](https://scimbe.github.io/CADS-DEMO-sort-docs/)** — live multi-agent sorting
  competition (`sort.bunsenbrenner.org`), source at
  [CADS-DEMO-sort](https://github.com/scimbe/CADS-DEMO-sort).
- **[Local LLM demo](https://scimbe.github.io/CADS-DEMO-local-llm-docs/)** — source at
  [CADS-DEMO-local-llm](https://github.com/scimbe/CADS-DEMO-local-llm).
- **[Kali desktop](https://scimbe.github.io/CADS-kali-desktop-docs/)** — full Kali Linux desktop
  streamed to the browser, gated by Keycloak (`kali.bunsenbrenner.org`), source at
  [CADS-kali-desktop](https://github.com/scimbe/CADS-kali-desktop).

## In integration

- **Deutschlandatlas call-center** — an n8n voice pipeline that answers questions from live
  Deutschlandatlas indicators (catalog match → live query → grounded phrasing → German TTS →
  STT), source at
  [CADS-DEMO-deutschlandatlas-callcenter](https://github.com/scimbe/CADS-DEMO-deutschlandatlas-callcenter).
  Under end-to-end verification.

## Documented, no dedicated site yet

- **[Code-Review demo](https://github.com/scimbe/CADS-DEMO-codereview)** — static-tool + LLM
  code review following the Harness Correction methodology; Phase 1 only, no VS Code
  integration yet.

## No public docs

- **game2048** (`game2048.bunsenbrenner.org`) — deployed, no dedicated repo or docs.
