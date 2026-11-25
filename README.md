# Traefik: ReadyStack build kit

The offline build kit for the Docker image `readystack/traefik:v3.7.19-CE-ubuntu24.04-r1`. It holds what the image is built from (upstream source, base images, toolchain, pinned dependencies) and the documents, so the image can be rebuilt with no network access (`docker build --network=none`).

- Release: [v3.7.19-CE-ubuntu24.04-r1](https://github.com/readystack-dev/traefik/releases/tag/v3.7.19-CE-ubuntu24.04-r1)
- Image: [`docker.io/readystack/traefik:v3.7.19-CE-ubuntu24.04-r1`](https://hub.docker.com/r/readystack/traefik/tags), manifest digest `sha256:065141024d7a6cae6a2288f425f0587511e9e913dd3db36ab51be39560047413`
- Product page: https://readystack.dev/products/traefik
- Defects: this repository's Issues (defects recorded from 25 November 2026 on) and https://readystack.dev/defects.html?product=traefik

Traefik is distributed under its upstream licence (MIT); the kit contains upstream's source under that licence, and each vendored component under its own.

## Download

From the [v3.7.19-CE-ubuntu24.04-r1 release](https://github.com/readystack-dev/traefik/releases/tag/v3.7.19-CE-ubuntu24.04-r1): `ReadyStack-traefik-v3.7.19-CE-ubuntu24.04-r1.tar.gz` (486.9 MB), SHA-256 `38732018554068adf02db0d234b4d189e38dab3141c6b397523f20ca994e821f`.

```sh
sha256sum --check --ignore-missing SHA256SUMS
```

## Verify and rebuild

The rebuild statement is signed with the ReadyStack release key. Check the signature before you rely on it:

```sh
cosign verify-blob --key https://readystack.dev/keys/cosign.pub \
  --bundle ReadyStack-traefik-v3.7.19-CE-ubuntu24.04-r1.rebuild.json.sigstore ReadyStack-traefik-v3.7.19-CE-ubuntu24.04-r1.rebuild.json
```

Unpack with `-p` so file modes are kept, then follow the kit's own README.md and docs/REBUILD.md:

```sh
tar -xpzf ReadyStack-traefik-v3.7.19-CE-ubuntu24.04-r1.tar.gz
cd v3.7.19-CE-ubuntu24.04-r1
less README.md docs/REBUILD.md
```

The build trail (`ReadyStack-traefik-v3.7.19-CE-ubuntu24.04-r1.trail.json`, signed the same way, and its Markdown copy) records how this release was built and checked.

## This repository

`recipe/` holds the kit's recipe files (Dockerfiles, build scripts, pins) as published at https://readystack.dev/products/traefik/recipe. The complete kit is the release download. Each release is a tag here.
