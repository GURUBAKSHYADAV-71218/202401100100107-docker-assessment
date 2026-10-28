
# FestPass — Docker & Container Security Assessment

- **Name:** Gurubaksh Yadav
- **REGNO:** 202401100100107
- **PORT:** 87
- **GitHub:** https://github.com/GURUBAKSHYADAV-71218/202401100100107-docker-assessment
- **Docker Hub:** https://hub.docker.com/r/gurubaksh/fest-backend

## Project Overview

FestPass is a containerised event-registration application using Docker, Docker Compose, PostgreSQL, Nginx, Trivy vulnerability scanning, Docker Hub image publishing, and Cosign image signing.

## Assessment Progress

- [x] Stage 1 — Containerise the backend
- [x] Stage 2 — Compose the three-tier stack
- [x] Stage 3 — Scan and fix vulnerabilities
- [x] Stage 4 — Publish versioned images
- [x] Stage 5 — Sign and verify images

## Stage 3 — Vulnerability Scanning and Hardening

The backend image was hardened using `python:3.13-slim-trixie`, updated Python dependencies, and a non-root application user.

Runtime verification:

```text
uid=10001(appuser) gid=10001(appuser) groups=10001(appuser)
```

### Trivy Results

The saved Trivy reports contain two `Total:` summaries per image. The combined figures below are the sums of those reported summaries.

| Image | Combined findings | CRITICAL | HIGH |
|---|---:|---:|---:|
| `fest-backend:0.1` — before | 13,381 | 234 | 2,250 |
| `fest-backend:0.2` — after | 400 | 9 | 97 |

Full scan outputs:
- Before: `reports/trivy-before.txt`
- After: `reports/trivy-after.txt`
- Gate output: `reports/trivy-gate.txt`

The configured gate command returned exit code `0`:

```powershell
trivy image --severity CRITICAL --ignore-unfixed --exit-code 1 fest-backend:0.2
```

The gate ignores unfixed vulnerabilities. The complete after-scan report still contains CRITICAL entries, so a passing gate does not mean the full report contains zero CRITICAL findings.

### Image Size Comparison

| Image | Docker disk usage | Content size |
|---|---:|---:|
| `fest-backend:0.1` | 1.61 GB | 412 MB |
| `fest-backend:0.2` | 195 MB | 47.9 MB |
| `gurubaksh/fest-backend:1.0.0` | 195 MB | 47.9 MB |

Sizes are from the local `docker image ls` output and may vary slightly across environments.

## Stage 4 — Published Images

- Docker Hub repository: https://hub.docker.com/r/gurubaksh/fest-backend
- Version `1.0.0` digest: `sha256:f9621d2cd9b41d5e34305a268cd976e05834085ddd78a9fc205d0dadda19e241`
- Version `1.0` points to the same image digest.
- The Compose backend uses `gurubaksh/fest-backend:1.0.0`.
- Deployment from the published image was tested; the backend reported version `1.0.0`, database connected, and both registrations remained available.

## Stage 5 — Cosign Image Signing

The published `1.0.0` image was signed and successfully verified against `cosign.pub`.

Verified digest:

`sha256:f9621d2cd9b41d5e34305a268cd976e05834085ddd78a9fc205d0dadda19e241`

The unsigned `1.0.1` image was pushed separately. Its digest is:

`sha256:417dcfbad5b689e0eb2ef15708683aee20f6cb0fc9ebb651ee2c2b919e18e9b6`

Verification of `1.0.1` returned `no signatures found`, as expected for an unsigned image.

## AI Tools Used

ChatGPT was used for command guidance, implementation assistance, troubleshooting, and explanations. All build, scan, deployment, signing, and verification results were checked through terminal output.

## Assessment Evidence

Add the assessment evidence PDF and any separately required screenshots before final submission. Keep secrets such as `.env`, `cosign.key`, and registry access tokens out of source control.
