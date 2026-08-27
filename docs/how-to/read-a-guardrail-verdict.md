# Read a guardrail verdict

The registry returns a `guardrail_verdict` on publish, and stores it. There are two shapes.

## `clean`

Nothing in the compose file would let the service reach out of its own bundle. The manifest is
catalogued and the dashboard shows a green badge.

## `flagged`

One or more violations, each tagged with the rule it broke and the offending value. This is a
real verdict from a bundle that asked for `privileged: true`, host networking, a world-exposed
port, and a host `/etc` mount:

```console
evil[F.1-non-loopback-port]: "0.0.0.0:9999:9999";
evil[F.2-privileged]: privileged: true;
evil[F.2-host-namespace]: network_mode: host;
evil[F.3-host-path-escapes-bundle]: /etc -> /etc (outside the bundle)
```

Each tag maps to a rule in [Guardrail rules](../reference/guardrail-rules.md):

| Tag | Meaning |
|---|---|
| `F.1-non-loopback-port` | a published port bound to something other than `127.0.0.1` |
| `F.2-privileged` | `privileged: true` |
| `F.2-host-namespace` | `network_mode: host` (or another host namespace) |
| `F.3-host-path-escapes-bundle` | a bind mount, build context, or env-file outside the bundle |

!!! note "Flagged does not mean rejected at publish"
    The registry **records** a flagged manifest with its verdict rather than refusing it. The
    refusal happens at activation time, against the operator's allowlist plus the same scan. Why
    the split exists is covered in [The trust model](../explanation/the-trust-model.md).
