# CADS Manifest Marketplace — documentation

Diátaxis-structured MkDocs Material site for
[scimbe/CADS-agent-marketplace](https://github.com/scimbe/CADS-agent-marketplace) — signed
service manifests + installer for ct-agent.

**Live:** https://scimbe.github.io/CADS-agent-marketplace-docs/

Its governing rule: every claim is marked **measured** (run end to end here), **audited** (run by
the maintainer, checked against source), or **not built** (deliberately absent). Nothing is
described that was not observed running.

## Local preview

```bash
pip install mkdocs-material
mkdocs serve
```

## Layout

- `docs/tutorials/` — learn by doing (publish your first manifest, with real console output)
- `docs/how-to/` — task recipes
- `docs/reference/` — the registry API, manifest schema, guardrail rules, installer kinds
- `docs/explanation/` — the trust model, the harness cage, the measured-vs-claimed discipline

Built and deployed by `.github/workflows/docs.yml` on every push to `main`.
