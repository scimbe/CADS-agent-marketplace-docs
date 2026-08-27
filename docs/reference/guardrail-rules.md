# Guardrail rules

The static scanner (`crates/installer-engine`, `guardrails.rs`) reads a compose file and refuses
the ways a service could break out of its own bundle. The **same** scanner runs at publish time
and at activation time.

## F.1 — Ports must be loopback

A published port bound to anything other than `127.0.0.1` is flagged as `F.1-non-loopback-port`.
A bare `"8080:8080"` or a `"0.0.0.0:…"` binding publishes on every interface and is refused;
`"127.0.0.1:8080:8080"` passes.

## F.2 — No privilege or host namespaces

| Flag | Refused as |
|---|---|
| `privileged: true` | `F.2-privileged` |
| `network_mode: host` (or other host namespace) | `F.2-host-namespace` |

## F.3 — Nothing may read outside the bundle

Three ways a compose file can reach host files, all refused unless they resolve inside the
bundle directory:

| Source | Rule |
|---|---|
| `volumes:` bind mount | `F.3-host-path-escapes-bundle` |
| `build.context` outside the bundle | `F.3-build-context-not-local` |
| `env_file:` outside the bundle | `F.3-env-file-escapes-bundle` |
| the docker socket (`/var/run/docker.sock`) | `F.3-docker-socket-mount` |

<div class="measured" markdown>
<span class="prov m">measured</span> A bundle carrying `privileged: true`, `network_mode: host`,
`0.0.0.0:9999:9999`, and a `/etc` bind mount was flagged on all four rules at publish time on
2026-08-27; a bundle with a single loopback port and a bundle-relative mount published `clean`.
</div>

!!! info "A fixed bypass, recorded honestly"
    The F.3 host-path check once failed **open** on an interpolated source: a volume written
    `${VAR:-/etc}` slipped past it and published as `clean` while mounting the host's `/etc`.
    Four of the project's own demo stacks used exactly that idiom. It was reported and fixed —
    the scanner now expands a default and, where it genuinely cannot resolve a value, refuses
    rather than allowing it. The measurement above is against the fixed scanner.
