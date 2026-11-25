# Provenance — Traefik (`v3.7.19-CE-ubuntu24.04-r1`)

Every item below is copied from a record in this archive; the record is named in brackets.

## Source

- Upstream repository: https://github.com/traefik/traefik
- Revision built: `v3.7.19` (commit `7dafd6dba371bd1aee70c443c2975c229c969711`)
- Source tree: `c07ba91b2cf34a1f7cc70fb1926065c46dd93bd14466f47b335f4ce8f0ad25c4` over 2332 files [`docs/provenance/SOURCE_TREE_HASH.json`]
- Community Edition only: enterprise code is removed or refused before the build (CE audit at acquire).

## Build

- Method: `source-rebuild-go`; base image `scratch + a chiselled Ubuntu 24.04 runtime (vendored/chisel/slices.txt, packages.tsv)`
- Compiled with `--network=none` from vendored source, dependencies and base images; BuildKit pinned to `moby/buildkit:v0.31.2` (shipped in `toolchain/`).
- SOURCE_DATE_EPOCH: `1794880034` [`SOURCE_DATE_EPOCH.txt`]
- Frozen inputs: [`docs/provenance/FREEZE_MANIFEST.json`], build recipe: [`docs/provenance/BUILD_RECIPE.json`].

## Output

- Published image: `readystack/traefik:v3.7.19-CE-ubuntu24.04-r1` — index digest `sha256:a674686beebf85ffc0503f3c4deb4d2a88a5dbd214e726637f457583cb02cf35` [`docs/provenance/LOCKED_DIGEST.txt`]
- linux/amd64 image digest `sha256:065141024d7a6cae6a2288f425f0587511e9e913dd3db36ab51be39560047413` — reproduced from this archive before publishing [`docs/provenance/REPRO_VERIFICATION.json`].

## Attestations and signature

- SBOM + SLSA provenance attached to the image: attached: an SPDX SBOM and SLSA v1 provenance [`docs/provenance/SCOUT_VERDICT.json`]
- cosign signature by the ReadyStack release key: verified before publishing. Check it: `cosign verify --key https://readystack.dev/keys/cosign.pub readystack/traefik:v3.7.19-CE-ubuntu24.04-r1`

## Pinned inputs, and why

- 41 pinned inputs, each with where it came from, why this one, how it was checked and when it was decided: `docs/PINS.md` [`docs/provenance/PINS.json`] (upstream source 2, base and build images 4, image digests 3, dependency versions readystack sets 11, pins in the kit's dockerfiles 14, readystack's build engine 5, dependencies upstream chose 2).

## Build trail

- How this release passed through the ReadyStack harness, record by record (every chain, drive, stage, review round, defect and check, each naming the record it is copied from): `ReadyStack-traefik-v3.7.19-CE-ubuntu24.04-r1.trail.json`, published beside this archive and signed with the release key (`ReadyStack-traefik-v3.7.19-CE-ubuntu24.04-r1.trail.json.sigstore`), and readable at https://readystack.dev/products/traefik/trail/v3.7.19-CE-ubuntu24.04-r1.

