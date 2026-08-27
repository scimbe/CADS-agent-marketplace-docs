# Installer kinds

`installer_kind` selects how a manifest is activated. `activate` matches on it exhaustively —
there is no fallback arm.

## `compose`

Brings up a Docker Compose stack. **Requires a running Docker daemon.**

<div class="notbuilt" markdown>
<span class="prov n">not run here</span> The compose activation path is real, but bringing a
stack up needs a container runtime, which the documenting host did not have. Everything up to
and including the guardrail scan was measured; the `docker compose up` itself was not.
</div>

## `binary`

Runs a hash-verified single executable, under the same bounded-process discipline as everything
else. The trust boundary shifts entirely onto the publisher allowlist — there is no compose YAML
to scan — which is documented as a **stronger** reliance, not a dropped check.

<div class="measured" markdown>
<span class="prov m">measured</span> The test that signs a binary, activates it, and captures its
stdout passes on a host with **no** Docker daemon — as does the whole workspace suite, 100 of
100, confirming the binary path no longer depends on a container runtime.
</div>

## `k8s`

<div class="notbuilt" markdown>
<span class="prov n">by design</span> Reserved in the schema, **not** executed. `activate`
rejects a `k8s` manifest outright with `unsupported_installer_kind`. No Kubernetes cluster
exists to prove an executor against, and shipping an unproven one would be a false claim.
</div>

## Payment { #payment }

<div class="notbuilt" markdown>
<span class="prov n">by design</span> Activations are recorded in a **ledger only**. The escrow-
hold primitive exists as a library with no production caller; no payment provider is called and
no money moves. The identity space already matches the tunnel's billing, so a later phase can add
real payment without an identity rewrite.
</div>
