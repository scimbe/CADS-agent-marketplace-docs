# Run the registry locally

The `registry` binary needs four environment variables and refuses to start without them — no
silent defaults on anything security-relevant.

```bash
export REGISTRY_BIND_ADDR=127.0.0.1:8787       # host:port to listen on
export REGISTRY_DB_PATH=./registry.db          # SQLite file, created if absent
export REGISTRY_BUNDLES_DIR=./bundles          # bundle store, created if absent
export REGISTRY_WRITE_TOKEN=<a strong token>   # required on POST routes; must not be blank
cargo run -p registry
```

!!! note "Same-origin, or no dashboard"
    The registry sends **no** CORS header. In a real deployment Caddy serves the dashboard and
    proxies the API under the same origin. Running the dashboard on a different port than the
    registry will fail in the browser — put a small same-origin proxy in front, or serve both
    behind one reverse proxy.

`GET /manifests` answers immediately with `[]`. See the [Registry API](../reference/registry-api.md)
for the full surface.
