# FestPass — Docker & Container Security Assessment

| | |
|---|---|
| Name | Himanshu Rana |
| REGNO | 202401100100119 |
| PORT | 8019 |
| Docker Hub username | himanshu3330 |
| Docker Hub repository | https://hub.docker.com/r/himanshu3330/fest-backend |

## AI tools used

- **Claude Code (Claude Opus 5.5)**: wrote the Dockerfile, `.dockerignore`, `compose.yaml` and this README. Ran the build, compose, Trivy and cosign commands, and helped read the Trivy results. It also found that the HIGH findings left in 0.2 came from libraries bundled inside pip.

## Stage 3: Trivy before and after

| Image | Base | CRITICAL | HIGH | Size |
|---|---|---|---|---|
| `fest-backend:0.1` | `python:3.9` (Debian 13.1) | 234 | 2249 (2243 OS + 6 Python) | 1.61 GB |
| `fest-backend:0.2` | `python:3.13.18-alpine3.24` | 0 | 0 | 96.6 MB |

Fixes in 0.2:

- Moved to a pinned Alpine base.
- Runs as the non-root user `festpass`.
- Upgraded Flask 2.2.2 → 3.1.3, Werkzeug 2.2.2 → 3.1.9 and psycopg2-binary 2.9.10 → 2.9.13.
- Uninstalled `pip` and `setuptools` after installing the requirements. pip 26.2.2 bundles vulnerable `urllib3` 2.7.0 and `msgpack` 1.1.2, and the app doesn't need pip at runtime.

`trivy image --severity CRITICAL --ignore-unfixed --exit-code 1 fest-backend:0.2` exits with code **0**.

Full reports: [reports/trivy-before.txt](reports/trivy-before.txt) and [reports/trivy-after.txt](reports/trivy-after.txt).

## Stage 4: published digest

`himanshu3330/fest-backend:1.0.0` (also tagged `1.0`) digest:

```
sha256:88162acba732bba1e9fcac615342b3daabc0508664791283c5913a1cc696d6b1
```

`compose.yaml` runs `himanshu3330/fest-backend:1.0.0` from Docker Hub instead of building it locally.

## Stage 5: cosign

- `cosign.pub` is committed. `cosign.key` is password-protected and ignored by `*.key` in `.gitignore`.
- `1.0.0` was signed by digest (`himanshu3330/fest-backend@sha256:88162acb…d6b1`). `cosign verify --key cosign.pub` passes.
- `1.0.1` (digest `sha256:61a741d724939ece5e5a2f66f1b696bcfe804feb9a888f487138cfc8d473c1dd`) was pushed unsigned. `cosign verify` rejects it with `no signatures found` (exit code 10).
- The key pair was regenerated while recording `S5-1`. `1.0.0` was then signed again with the new key, so the committed `cosign.pub` verifies it.

## Screenshots

All evidence is in [`screenshots/`](screenshots): `S1-2`, `S1-3`, `S2-2`, `S2-3`, `S2-4`, `S3-1`, `S3-3`, `S4-1`, `S4-2`, `S4-3` (terminal) with `S4-3-browser` (page), `S5-1`, `S5-2`, `S5-3` and `S5-4`.

## Steps not completed

_None._
