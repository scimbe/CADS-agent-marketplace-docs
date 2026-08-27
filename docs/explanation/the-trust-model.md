# The trust model

A manifest tells a ct-agent what to build and run. That makes it a remote-code-execution
primitive, and the whole design follows from taking that seriously.

## Three gates, each able to refuse

A manifest reaches a container runtime only if all three pass. Each is independent.

1. **The signature must verify.** Every manifest is ed25519-signed by its publisher's holder
   key. The registry recomputes the channel binding itself rather than trusting a caller-supplied
   id, so a forged or altered manifest is rejected at submit time — not discovered later.

2. **The publisher must be allow-listed.** A good signature only proves *who* signed. It never
   implies trust. The installer checks the publisher against an explicit, operator-maintained
   allowlist — never trust-on-first-use.

3. **The bundle must survive the guardrail scan.** Before anything runs, the compose file is
   statically scanned for the ways a container can escape its box. See
   [Guardrail rules](../reference/guardrail-rules.md).

## Why the registry records a flagged manifest instead of refusing it

The registry stores a flagged manifest *with* its verdict rather than rejecting it at publish.
That is deliberate: the registry is a catalogue, and a visible, recorded verdict is more useful
than a silent rejection. The **refusal** belongs at activation time, where the operator's
allowlist and the same scan decide whether this specific agent will run this specific bundle.

The one thing that must never happen is a check that passes *silently* when it should have
flagged — a guard that fails open produces a false assurance, which is worse than no guard. That
is exactly the failure the F.3 interpolation bug was, and why fixing it mattered more than its
small footprint suggested.

## A valid signature is necessary and never sufficient

If you take one thing from this page: the signature answers *who*, the guardrail answers *what*,
and the allowlist answers *whether this agent trusts them*. Three different questions. A manifest
that answers only the first gets nowhere.
