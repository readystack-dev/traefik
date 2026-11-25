# Traefik v3.8.0 — ReadyStack release `v3.8.0-CE-ubuntu24.04-r1`

Generated 2026-11-25 from this release's build records and strategy. Statements that come from a single record name it in brackets; the others come from this release's strategy and build log.

## Release

- **Image:** `readystack/traefik:v3.8.0-CE-ubuntu24.04-r1` (linux/amd64). Index digest `sha256:06530d73d21362c91cb017a9fdc8995945e2b7744ef335a82a8d266356d5e213` [`docs/provenance/LOCKED_DIGEST.txt`].
- **Image manifest digest:** `sha256:a7980372e214b6e7f5977a1c83fa495da0bb2c54dd5ae88a101398776962dfc5` [`TARGET_DIGEST.txt`].
- **Upstream:** Traefik v3.8.0 built from source at `v3.8.0` of https://github.com/traefik/traefik — upstream notes: https://github.com/traefik/traefik/releases/tag/v3.8.0 [`docs/provenance/BUILD_RECIPE.json`].
- **Base image:** none (`FROM scratch`): a chiselled Ubuntu 24.04 runtime, the files Canonical's chisel cut from Ubuntu's own packages (`vendored/chisel/slices.txt`, `vendored/chisel/packages.tsv`); runtime user `traefik`.
- **Build:** `source-rebuild-go`, compiled offline with `--network=none`.
- **Supersedes:** ReadyStack release `v3.7.19-CE-ubuntu24.04-r1`.

## What changed in this release

- Upstream version v3.7.19 → v3.8.0.
- Minimum dependency versions ReadyStack enforces on top of upstream (raised only where upstream's own version was lower); shipped versions from the SBOM [`docs/sbom/SBOM.json`]:
  - `golang.org/x/crypto` at least `v0.58.0`: not raised, upstream already pins `v0.59.0` (shipped: `v0.59.0`)
  - `golang.org/x/net` at least `v0.62.0`: not raised, upstream already pins `v0.63.0` (shipped: `v0.63.0`)
  - `golang.org/x/sys` at least `v0.49.0`: not raised, upstream already pins `v0.50.0` (shipped: `v0.50.0`)
  - `golang.org/x/text` at least `v0.43.0`: not raised, upstream already pins `v0.44.0` (shipped: `v0.44.0`)
  - `google.golang.org/grpc` at least `v1.83.2`: not raised, upstream already pins `v1.85.0` (shipped: `v1.85.0`)
  - `github.com/andybalholm/brotli` at least `v1.2.7`: not raised, upstream already pins `v1.2.7` (shipped: `v1.2.7`)
  - `github.com/klauspost/compress` at least `v1.20.3`: not raised, upstream already pins `v1.20.4` (shipped: `v1.20.4`)
- This release moves Traefik from 3.7.19 to 3.8.0, a release of upstream's newer 3.8.x line. Upstream's release notes list what changed: https://github.com/traefik/traefik/releases/tag/v3.8.0

## Security status at release (measured 2026-11-25)

- **Docker Scout:** 0 fixable critical/high vulnerabilities; runs as `traefik` (non-root); SBOM + provenance attestations present (SPDX SBOM + SLSA v1) [`docs/provenance/SCOUT_VERDICT.json`].
- **OSV cross-check:** no fixable critical/high vulnerability beyond Docker Scout's findings.
- **Signature:** cosign signature verified by ReadyStack's release gate before publishing; check it yourself with `cosign verify --key https://readystack.dev/keys/cosign.pub readystack/traefik:v3.8.0-CE-ubuntu24.04-r1` (signatures are recorded in the public Sigstore transparency log).
- **ReadyStack release check at release:** passed (ReadyStack's own test, not a Docker Scout grade). Vulnerabilities are disclosed every day and the image never changes, so it is re-checked daily, beside Docker Scout's own dated grade, at https://readystack.dev/queue.

## Reproducibility

Rebuilds offline from this archive to the identical image digest `sha256:a7980372e214b6e7f5977a1c83fa495da0bb2c54dd5ae88a101398776962dfc5`: run `./BUILD_OFFLINE.sh`, then `./VERIFY_OFFLINE.sh` (add `--online` to confirm the Docker Hub tag holds the same digest). Before publishing, ReadyStack rebuilt the kit with `./BUILD_OFFLINE.sh` and compared the result with the published digest [`docs/provenance/REPRO_VERIFICATION.json`], then ran `./BUILD_OFFLINE.sh` and `./VERIFY_OFFLINE.sh` from this archive (without `--online`).

## Validation

- 5 of 5 use cases passed (5 authored) against the running image [`docs/validation/test-results.json`].

## Known issues

- None found (reviewed by held semantic review (Claude) on 2026-11-25).

## Defects fixed

- 36 problems found before release and fixed before it shipped (36 by review); each is listed in `docs/defects/defects.json`.

## Verify this archive

```
sha256sum -c CHECKSUMS.sha256
./BUILD_OFFLINE.sh && ./VERIFY_OFFLINE.sh --online
cosign verify --key https://readystack.dev/keys/cosign.pub readystack/traefik:v3.8.0-CE-ubuntu24.04-r1
```

ReadyStack records every signature in the public Sigstore transparency log (Rekor), so the command needs no extra flags.

