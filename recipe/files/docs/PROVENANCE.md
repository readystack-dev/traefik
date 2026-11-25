# Provenance — Traefik (`v3.8.0-CE-ubuntu24.04-r1`)

Every item below is copied from a record in this archive; the record is named in brackets.

## Source

- Upstream repository: https://github.com/traefik/traefik
- Revision built: `v3.8.0` (commit `644b1f19cf031ec079cca10fad0c86e5ab7181b7`)
- Source tree: `d6b4d17cdaa0b58c0bae0dc46a4df80257ccfc1e29a0b633783a22e154c58cfa` over 2431 files [`docs/provenance/SOURCE_TREE_HASH.json`]
- Community Edition only: enterprise code is removed or refused before the build (CE audit at acquire).

## Build

- Method: `source-rebuild-go`; base image `scratch + a chiselled Ubuntu 24.04 runtime (vendored/chisel/slices.txt, packages.tsv)`
- Compiled with `--network=none` from vendored source, dependencies and base images; BuildKit pinned to `moby/buildkit:v0.31.2` (shipped in `toolchain/`).
- SOURCE_DATE_EPOCH: `1795577779` [`SOURCE_DATE_EPOCH.txt`]
- Frozen inputs: [`docs/provenance/FREEZE_MANIFEST.json`], build recipe: [`docs/provenance/BUILD_RECIPE.json`].

## Output

- Published image: `readystack/traefik:v3.8.0-CE-ubuntu24.04-r1` — index digest `sha256:06530d73d21362c91cb017a9fdc8995945e2b7744ef335a82a8d266356d5e213` [`docs/provenance/LOCKED_DIGEST.txt`]
- linux/amd64 image digest `sha256:a7980372e214b6e7f5977a1c83fa495da0bb2c54dd5ae88a101398776962dfc5` — reproduced from this archive before publishing [`docs/provenance/REPRO_VERIFICATION.json`].

## Attestations and signature

- SBOM + SLSA provenance attached to the image: attached: an SPDX SBOM and SLSA v1 provenance [`docs/provenance/SCOUT_VERDICT.json`]
- cosign signature by the ReadyStack release key: verified before publishing. Check it: `cosign verify --key https://readystack.dev/keys/cosign.pub readystack/traefik:v3.8.0-CE-ubuntu24.04-r1`

## Pinned inputs, and why

- 42 pinned inputs, each with where it came from, why this one, how it was checked and when it was decided: `docs/PINS.md` [`docs/provenance/PINS.json`] (upstream source 2, base and build images 4, image digests 3, dependency versions readystack sets 11, pins in the kit's dockerfiles 15, readystack's build engine 5, dependencies upstream chose 2).

## Build trail

- How this release passed through the ReadyStack harness, record by record (every chain, drive, stage, review round, defect and check, each naming the record it is copied from): `ReadyStack-traefik-v3.8.0-CE-ubuntu24.04-r1.trail.json`, published beside this archive and signed with the release key (`ReadyStack-traefik-v3.8.0-CE-ubuntu24.04-r1.trail.json.sigstore`), and readable at https://readystack.dev/products/traefik/trail/v3.8.0-CE-ubuntu24.04-r1.

