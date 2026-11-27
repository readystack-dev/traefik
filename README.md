[![ReadyStack](https://readystack.dev/logo.svg)](https://readystack.dev)

**Stable, Open-source Releases, Automated** · [readystack.dev](https://readystack.dev)

[![Docker Hub](https://img.shields.io/badge/Docker%20Hub-readystack%2Ftraefik-2496ED?logo=docker&logoColor=white)](https://hub.docker.com/r/readystack/traefik) [![Docker pulls](https://img.shields.io/docker/pulls/readystack/traefik)](https://hub.docker.com/r/readystack/traefik) [![Grade at release](https://img.shields.io/badge/grade%20at%20release-A%20%2894%25%29-2ea44f)](https://readystack.dev/products/traefik) [![Signed: cosign](https://img.shields.io/badge/signed-cosign-2ea44f)](https://readystack.dev/keys/cosign.pub) ![SBOM: SPDX](https://img.shields.io/badge/SBOM-SPDX-2ea44f) ![Provenance: SLSA v1](https://img.shields.io/badge/provenance-SLSA%20v1-2ea44f) ![Offline rebuild: verified](https://img.shields.io/badge/offline%20rebuild-verified-2ea44f)

# Traefik: ReadyStack build kit

The offline build kit for the Docker image `readystack/traefik:v3.8.0-CE-ubuntu24.04-r1`. It holds what the image is built from (upstream source, base images, toolchain, pinned dependencies) and the documents, so the image can be rebuilt with no network access (`docker build --network=none`).

- Release: [v3.8.0-CE-ubuntu24.04-r1](https://github.com/readystack-dev/traefik/releases/tag/v3.8.0-CE-ubuntu24.04-r1)
- Image: [`docker.io/readystack/traefik:v3.8.0-CE-ubuntu24.04-r1`](https://hub.docker.com/r/readystack/traefik/tags), manifest digest `sha256:a7980372e214b6e7f5977a1c83fa495da0bb2c54dd5ae88a101398776962dfc5`
- Product page: https://readystack.dev/products/traefik
- Source and build recipe: https://readystack.dev/products/traefik/recipe
- Build trail: https://readystack.dev/products/traefik/trail/v3.8.0-CE-ubuntu24.04-r1
- Defects: this repository's Issues (defects recorded from 25 November 2026 on) and https://readystack.dev/defects.html?product=traefik

## The image

Single container, single static binary, foreground process. No relational database, no user accounts. Only the FILE provider is enabled by default (--providers.file.filename=/etc/traefik/traefik-dynamic.yml, baked into the image's ENTRYPOINT; the shipped file is an empty configuration, {}); the docker, kubernetes and other providers are off until you add their flag as an extra command-line argument (see Docker provider in the handbooks). Mounting /var/run/docker.sock by itself changes nothing (tested).

## Who uses Traefik and why

Teams running services on container platforms such as Kubernetes who want routing that follows services as they change.

Traefik is an HTTP reverse proxy and load balancer that reads your infrastructure, such as Kubernetes, and configures its routes automatically and dynamically, including HTTPS certificates.

A typical use: A team deploys new microservices to Kubernetes every week. Each service declares its host name in its own configuration; Traefik notices it, starts routing traffic to it and obtains a certificate for it, with no edits to a central proxy configuration.

From the upstream project's own description: https://github.com/traefik/traefik.

## This release

- Docker Scout policy grade at release: **A** (94%), graded 2026-11-25 with `docker scout policy` (CLI 1.26.0, docker-default configuration).
  - Docker Scout counts 9 packages with copyleft licences (GPL, LGPL, MPL), all of them operating-system packages (it counts only packages whose licence it can read; the SBOM lists every component).
- Signed with ReadyStack's release key (fingerprint `04daa92344a52dbc`): https://readystack.dev/keys/cosign.pub.
- Runs as a non-root user (`traefik`).
- Attestations on the image: an SPDX SBOM and SLSA provenance v1.
- Rebuilt from this kit with network egress blocked on 2026-11-25; the rebuilt image digest is the published one.
- Base: Ubuntu 24.04.

The current grade, measured again every day, is on https://readystack.dev/products/traefik.

Traefik is distributed under its upstream licence (MIT); the kit contains upstream's source under that licence, and each vendored component under its own.

## Download

From the [v3.8.0-CE-ubuntu24.04-r1 release](https://github.com/readystack-dev/traefik/releases/tag/v3.8.0-CE-ubuntu24.04-r1): `ReadyStack-traefik-v3.8.0-CE-ubuntu24.04-r1.tar.gz` (524.9 MB), SHA-256 `c759a2aec101870bf3d6713f40db4cffd5d20fd1061519f67c6c14f88de39b8c`.

```sh
sha256sum --check --ignore-missing SHA256SUMS
```

## Verify and rebuild

The rebuild statement is signed with the ReadyStack release key. Check the signature before you rely on it:

```sh
cosign verify-blob --key https://readystack.dev/keys/cosign.pub \
  --bundle ReadyStack-traefik-v3.8.0-CE-ubuntu24.04-r1.rebuild.json.sigstore ReadyStack-traefik-v3.8.0-CE-ubuntu24.04-r1.rebuild.json
```

Unpack with `-p` so file modes are kept, then follow the kit's own README.md and docs/REBUILD.md:

```sh
tar -xpzf ReadyStack-traefik-v3.8.0-CE-ubuntu24.04-r1.tar.gz
cd v3.8.0-CE-ubuntu24.04-r1
less README.md docs/REBUILD.md
```

The build trail (`ReadyStack-traefik-v3.8.0-CE-ubuntu24.04-r1.trail.json`, signed the same way, and its Markdown copy) records how this release was built and checked.

## This repository

`recipe/` holds the kit's recipe files (Dockerfiles, build scripts, pins) as published at https://readystack.dev/products/traefik/recipe. The complete kit is the release download. Each release is a tag here. How to report a vulnerability: [SECURITY.md](SECURITY.md).

## About ReadyStack

**Stable, Open-source Releases, Automated.** ReadyStack releases popular open-source apps every week with every available critical and high security fix applied, plus full documentation, reference data, tests and a build recipe that recreates each release bit-for-bit. Each release is rebuilt from its pinned inputs with the network off, checked against its published digest, signed, and scanned and graded again every day. The images and build kits are free, and ReadyStack is soon to be a nonprofit.

[readystack.dev](https://readystack.dev) · [All products](https://readystack.dev/products.html) · [Build kits on GitHub](https://github.com/readystack-dev) · [Defect log](https://readystack.dev/defects.html) · contact@readystack.dev

— ReadyStack · https://readystack.dev
