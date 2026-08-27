# Measured, not claimed

Every page here marks its statements. The marks are not decoration — they encode how much you
should trust each claim, and they exist because a manifest is dangerous enough that "it probably
works" is not good enough.

| Mark | Means |
|---|---|
| <span class="prov m">measured</span> | Run end to end on the documenting host — no Docker daemon, 2026-08-27, marketplace at `d09eecd`. The output was seen and is quoted verbatim. |
| <span class="prov a">audited</span> | Run by the maintainer against a real model. Transcripts and guard strings checked against source independently. Not reproduced here. |
| <span class="prov n">not built</span> | Deliberately absent — reserved in the schema, refused at runtime, and documented as such. |

## Why the distinction is load-bearing

Three of the failures found in this project were **silent by construction**: a guardrail that
failed open produced the same `clean` verdict as a safe bundle; a test suite could not see a
Docker-only failure because its CI runner shipped a daemon; a bridge served `200` on every route
while its real function was dead. In each case a green result meant nothing, and only someone who
distrusted the green result on purpose found the truth.

So the tests that earn a <span class="prov m">measured</span> mark here are the ones designed to
fail loudly:

- **For a guard:** turn it off and confirm the test fails. A guard never watched failing is
  unproven.
- **For a liveness signal:** run a control arm. A value that reads healthy during a real outage
  teaches its readers to ignore it.
- **For a monitoring gap:** ask what a green check would look like *during* the failure. If the
  answer is "identical", that is the finding.

Nothing on this site is written as working that was only read in the source, and nothing planned
is written as if it shipped. Where a claim could only be checked from the outside — the deployed
commit, for instance — the honest limit is stated rather than the reassuring version.
