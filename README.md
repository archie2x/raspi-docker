## Raspberry Pi OS Docker images

GitHub Actions workflow [`build-raspios.yml`](.github/workflows/build-raspios.yml) converts the
published Raspberry Pi OS Lite arm64 image into container-friendly layers and pushes them to GHCR.

### What gets built

- `ghcr.io/<owner>/raspios:trixie-arm64` – a faithful reproduction of the upstream rootfs.
- `ghcr.io/<owner>/raspios:trixie-arm64-YYYY-MM-DD` – the same filesystem, tagged with the
  release date that Raspberry Pi publishes (parsed from the resolved download URL).
- `ghcr.io/<owner>/raspios-build-box:*` – adds a small set of development tools
  (`build-essential`, `git`, `curl`, `cmake`, `pkg-config`, `python3`, etc.) on top of the dated base
  image so the toolchain always matches the underlying OS.

You can change the default packages via the `BUILD_BOX_PACKAGES` environment entry in the workflow.

### Version visibility

Before downloading, the workflow resolves `raspios_lite_arm64_latest` to the concrete artifact
(`2025-11-24-raspios-trixie-arm64-lite.img.xz`, for example) and records the release date. It also
reads `/etc/os-release` from the mounted image so logs show the upstream `VERSION`, `VERSION_ID`,
and `BUILD_DATE`. Use the dated tag when you need to pin an exact bit-for-bit image.

### Tagging best practices

Docker images commonly carry multiple tags that all point to the same digest. For example:

- `raspios:trixie-arm64` – acts like a “minor latest” for that distro/cpu pair and is updated in
  place.
- `raspios:trixie-arm64-2025-11-24` – immutable tag tied to a specific upstream build.
- `raspios:latest` (optional) – only publish this if you want a single default entry point; keep in
  mind that it should always be safe to pull and therefore should match whichever variant you wish
  to highlight.

This pattern lets consumers choose stability (`YYYY-MM-DD` tags) or convenience (rolling tags) while
still referencing the same pushed manifest.
