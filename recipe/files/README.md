<!-- readystack:kit-readme begin (written at packaging; the kit's own README follows the end marker) -->

# Build kit for `readystack/traefik:v3.8.0-CE-ubuntu24.04-r1`

This archive rebuilds the linux/amd64 image `readystack/traefik:v3.8.0-CE-ubuntu24.04-r1` from its own contents, with the network off, and checks the result against the image digest ReadyStack published, `sha256:a7980372e214b6e7f5977a1c83fa495da0bb2c54dd5ae88a101398776962dfc5` (also in `TARGET_DIGEST.txt`). It holds everything the build needs, not the image itself.

## Rebuild it

You need linux/amd64 Docker with `buildx` 0.30 or later, `jq` and `tar` (docs/REBUILD.md lists the rest, if any, and the hardware ReadyStack used).

```
tar -xpzf ReadyStack-traefik-v3.8.0-CE-ubuntu24.04-r1.tar.gz    # -p keeps each file's mode
cd v3.8.0-CE-ubuntu24.04-r1
./BUILD_OFFLINE.sh            # builds with the network off, from this archive alone
./VERIFY_OFFLINE.sh           # compares the rebuilt digest with the published one, then checks the image
```

`./VERIFY_OFFLINE.sh --online` also checks that the Docker Hub tag holds the digest you rebuilt.

File modes are build input: a Dockerfile `COPY` carries them into the image. Unpacked without `-p`, your umask changes them (664 becomes 644 under umask 022). `FILE_MODES.tsv` records the mode of every file and directory as packed; `BUILD_OFFLINE.sh` puts back any that differ before it builds, and `./BUILD_OFFLINE.sh --restore-modes` does only that.

## Check ReadyStack's signature (optional)

ReadyStack signs this archive's `rebuild.json` with its release key once the finished archive has passed the release gate, and records the signature in the public Sigstore transparency log. The signature is made after the archive is sealed, so it is published beside it rather than inside it. Download it and the public key next to this directory, not into it: a file added inside the kit changes the tree hash that `rebuild.json` records, and `validate` then refuses the kit.

```
curl -fsSLo ../cosign.pub https://readystack.dev/keys/cosign.pub
curl -fsSLo ../ReadyStack-traefik-v3.8.0-CE-ubuntu24.04-r1.rebuild.json.sigstore https://readystack.dev/api/kit/traefik/v3.8.0-CE-ubuntu24.04-r1/files/ReadyStack-traefik-v3.8.0-CE-ubuntu24.04-r1.rebuild.json.sigstore
python3 docs/rebuild-bundle/rebuild_bundle.py validate . --key ../cosign.pub --signature ../ReadyStack-traefik-v3.8.0-CE-ubuntu24.04-r1.rebuild.json.sigstore
```

`validate` needs Python 3 and cosign. Without building anything it checks the signature over `rebuild.json`, every file's checksum, the tree hash over every file's path, mode and content, each base image layout's digest and the builder archive. `verify`, with the same arguments, also rebuilds with the builder's network egress blocked and exits 0 only when the rebuilt digest is the published one (docs/REBUILD.md).

## What else is here

- `docs/REBUILD.md`: the full rebuild guide: requirements, what `rebuild.json` records, the verifier's exit codes, and SLSA provenance on your own build platform
- `docs/VERIFY.md`: checking the published image, its signature and its attestations without rebuilding
- `docs/PROVENANCE.md`: where every input came from
- `docs/PINS.md`: every pinned input and why it was chosen
- `docs/RELEASE_NOTES.md`: what this release changed, and its known issues
- `docs/admin-guide/ADMIN_HANDBOOK.md`: running the product

<!-- readystack:kit-readme end -->

# ReadyStack Traefik build kit

Traefik v3.8.0 (MIT) — reverse proxy / load balancer. Single static Go binary
(`cmd/traefik`) with the dashboard embedded via `//go:embed static`. No database;
config/provider-driven. Runs **non-root** (`setcap cap_net_bind_service` lets it
bind :80/:443 without root) on a chiselled Ubuntu 24.04 runtime: `FROM scratch` with only the files Canonical's
chisel cut from a few Ubuntu packages (no apt, dpkg, bash or curl).

## Files
| File | Role |
|---|---|
| `Dockerfile.vendor` | **Online** vendor stage. Builds the dashboard (node24 + yarn4 → `webui/static`), copies it into a fresh traefik v3.8.0 checkout, raises grpc + `golang.org/x/{crypto,net,sys,text}` + `go-jose/v4`, `andybalholm/brotli` and `klauspost/compress` to exact pinned floors (never `@latest`, never a downgrade; `held()` re-checks each after `go mod tidy`), `go mod vendor`, and cuts the runtime rootfs with Canonical's chisel (pinned release and chisel-releases commit, checksummed) from Ubuntu's own .debs, generating its dpkg status and failing if a package of the cut is below the kit's package floors. Runs on the same pinned go1.27.4 toolchain as the offline build (v3.8.0's go.mod requires Go 1.27). `--target export` emits `./vendored/{traefik-src,chisel}`. |
| `Dockerfile.offline.pinned` | **L-009 hermetic** build. `--network=none` from the vendored tree with the digest-pinned golang 1.27.4 builder (BASE_IMAGE_DIGESTS.lock): builds traefik and rs-probe (the HEALTHCHECK helper, `rs-probe.go`), lays the chiselled rootfs out on the pinned ubuntu:24.04 with the traefik user (uid 999), gives `/usr/bin/traefik` cap_net_bind_service with the cut's own setcap, and builds the image `FROM scratch` from it. Stops unless the binary was built with go1.27.4 or later and reports 3.8.0. Produces the attested DockerHub image (SBOM + SLSA-max provenance). |
| `Dockerfile`, `Dockerfile.offline` | Copies of `Dockerfile.offline.pinned` (the scratch boot proof builds the same image). |
| `rs-probe.go`, `rs-probe.mod` | The HEALTHCHECK helper: `rs-probe URL` exits 0 on a 2xx/3xx answer (curl -f), `-body` prints the body; standard library only. |
| `traefik-dynamic.yml` | Minimal valid dynamic config so the boot/health probe passes. |

## Build (what the orchestrator runs)
```
# assemble_kit (online): vendor + scratch boot proof
docker build -f Dockerfile.vendor --target export --output type=local,dest=./vendored .
docker build -t traefik-scratch .

# build_kit (offline, attested): the canonical image
docker buildx build --builder readystack-builder-max --network=none \
  --attest type=provenance,mode=max --attest type=sbom \
  -f Dockerfile.offline.pinned -t readystack/traefik:<tag> --push <git-context>
```

## Health
`GET http://127.0.0.1:8080/ping` → `200 OK` (the `traefik` entrypoint, which `--ping=true` creates on :8080; it keeps answering with `--api.insecure=false`); the image's HEALTHCHECK runs `/usr/local/bin/rs-probe http://127.0.0.1:8080/ping`. REST API + dashboard on the same port.
