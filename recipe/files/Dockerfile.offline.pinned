# =====================================================================
# HERMETIC OFFLINE BUILD, chiselled runtime (canonical, attestation-producing): built by `docker buildx build
#   --network=none --attest type=provenance,mode=max --attest type=sbom`. Every input is vendored or pinned by
#   digest: the Go builder and ubuntu:24.04 (the rootfs assembly stage only), vendored/traefik-src (the fully
#   vendored module tree, GOPROXY=off) and vendored/chisel (the runtime rootfs Canonical's chisel cut from
#   Ubuntu's own .debs: rootfs.tar, debs/, packages.tsv, slices.txt). The image is FROM scratch with that rootfs
#   alone: no apt, dpkg, bash or curl. Dockerfile and Dockerfile.offline are copies of this file.
# =====================================================================
FROM golang:1.27.4-bookworm@sha256:4fa7c968ba93f1635670522dbb01a2c78150443e1385746924566079f36570ea AS build
WORKDIR /src
COPY vendored/traefik-src/ .
ENV CGO_ENABLED=0 GOTOOLCHAIN=local GOFLAGS="-mod=vendor -buildvcs=false" GOPROXY=off
RUN go build -trimpath -ldflags='-s -w -X github.com/traefik/traefik/v3/pkg/version.Version=3.8.0' \
      -o /traefik ./cmd/traefik
# Fail closed on a stale toolchain. The digest this kit used to pin under the floating 1.26-bookworm tag
# was go1.26.4 (published 2026-06-11) long after the tag had moved on, so chain 491 shipped
# stdlib 1.26.4 with 7 fixable critical/high (fixed in 1.26.6). `go version <binary>` reads the
# toolchain recorded in the binary itself; anything older than go1.27.4 stops the build.
RUN v=$(go version /traefik | awk '{print $2}') && echo "traefik built with $v" && \
    [ "$(printf '%s\n%s\n' go1.27.4 "$v" | sort -V | head -n1)" = go1.27.4 ] || \
    { echo "traefik was built with '$v', older than the go1.27.4 floor: the golang base digest is stale" >&2; exit 1; }
RUN /traefik version | tee /tmp/traefik.version && grep -Eq '^Version:[[:space:]]+3\.8\.0$' /tmp/traefik.version || \
    { echo "built traefik does not report 3.8.0" >&2; exit 1; }

# ---- rs-probe: the HEALTHCHECK helper (standard library only, from the kit's rs-probe.go, the engine's
# generators/gen_dockerfile.py RS_PROBE_GO): the image has no curl, whose library closure alone costs ~21 medium findings
FROM golang:1.27.4-bookworm@sha256:4fa7c968ba93f1635670522dbb01a2c78150443e1385746924566079f36570ea AS build_rs_probe
WORKDIR /probe
COPY rs-probe.go main.go
COPY rs-probe.mod go.mod
ENV CGO_ENABLED=0 GOTOOLCHAIN=local GOFLAGS="-buildvcs=false" GOPROXY=off SOURCE_DATE_EPOCH=1795577779
RUN go build -trimpath -ldflags='-s -w' -o /rs-probe .

# ---- rootfs: the chiselled Ubuntu 24.04 runtime (vendored/chisel, cut by Dockerfile.vendor's chiselcut stage from
# Ubuntu's own .debs), the service user, the binary and its configuration, laid out on the pinned base. Only /rootfs
# reaches the image. setcap runs from the cut itself (libcap2-bin_bins) under chroot: traefik binds :80/:443 as the
# non-root traefik user also where the runtime keeps net.ipv4.ip_unprivileged_port_start at 1024; the file
# capability travels as the security.capability xattr.
FROM ubuntu:24.04@sha256:9c7f200c07e3d4a03e7249d2c16bf66a51120fb876aa2242cf31451412543289 AS rootfs
ARG SOURCE_DATE_EPOCH=1795577779
ADD vendored/chisel/rootfs.tar /rootfs/
RUN printf 'traefik:x:999:999::/var/lib/traefik:/bin/sh\n' >> /rootfs/etc/passwd && \
    printf 'traefik:x:999:\n' >> /rootfs/etc/group && \
    mkdir -p /rootfs/etc/traefik /rootfs/var/lib/traefik && chown -R 999:999 /rootfs/etc/traefik /rootfs/var/lib/traefik && chmod 750 /rootfs/var/lib/traefik
COPY --from=build /traefik /rootfs/usr/bin/traefik
COPY --from=build_rs_probe /rs-probe /rootfs/usr/local/bin/rs-probe
COPY traefik-dynamic.yml /rootfs/etc/traefik/traefik-dynamic.yml
RUN chmod 755 /rootfs/usr/bin/traefik /rootfs/usr/local/bin/rs-probe && \
    chown 999:999 /rootfs/etc/traefik/traefik-dynamic.yml && \
    chroot /rootfs /usr/sbin/setcap cap_net_bind_service=+ep /usr/bin/traefik && \
    { chroot /rootfs /usr/sbin/getcap /usr/bin/traefik | grep -q 'cap_net_bind_service=ep' || \
      { echo "setcap did not give /usr/bin/traefik cap_net_bind_service" >&2; exit 1; }; }
# The scanners' inputs must be in the image: /etc/os-release and a dpkg status that names every package of the cut
# (chisel writes none; Dockerfile.vendor generates it). The wall-clock and host-derived paths go.
RUN --mount=type=bind,source=vendored/chisel,target=/chisel set -eu; \
    [ -e /rootfs/etc/os-release ] || { echo "no /etc/os-release: base-files_release-info is missing" >&2; exit 1; }; \
    [ "$(grep -c '^Package: ' /rootfs/var/lib/dpkg/status)" = "$(wc -l < /chisel/packages.tsv)" ] || \
      { echo "/var/lib/dpkg/status does not list every package of vendored/chisel/packages.tsv" >&2; exit 1; }; \
    rm -rf /rootfs/var/log/dpkg.log /rootfs/var/log/alternatives.log /rootfs/var/log/apt /rootfs/var/cache/ldconfig/aux-cache /rootfs/var/cache/fontconfig

FROM scratch
ARG SOURCE_DATE_EPOCH=1795577779
COPY --from=rootfs /rootfs/ /
ENV PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HEALTHCHECK --interval=30s --timeout=10s --start-period=10s \
  CMD ["/usr/local/bin/rs-probe", "http://127.0.0.1:8080/ping"]
USER traefik
EXPOSE 80 443 8080
ENTRYPOINT ["traefik","--ping=true","--api.insecure=true","--api.dashboard=true","--entrypoints.web.address=:80","--entrypoints.websecure.address=:443","--providers.file.filename=/etc/traefik/traefik-dynamic.yml"]
# >>> readystack OCI labels: strategies/traefik.json image_labels plus the standard OCI labels,
# the set a generated kit carries (gen_dockerfile.label_lines). Written by scripts/kit_migrations.py;
# scripts/retarget.py re-renders it with every retarget. Never edit it by hand.
LABEL org.opencontainers.image.licenses="MIT"
LABEL org.opencontainers.image.title="Traefik"
LABEL org.opencontainers.image.source="https://github.com/traefik/traefik"
LABEL org.opencontainers.image.version="v3.8.0-CE-ubuntu24.04-r1"
LABEL org.opencontainers.image.revision="644b1f19cf031ec079cca10fad0c86e5ab7181b7"
# <<< readystack OCI labels
