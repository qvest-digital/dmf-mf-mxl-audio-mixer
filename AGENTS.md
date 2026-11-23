# dmf-mf-mxl-audio-mixer - working rules

A fork of LeeO86/mxl-audio-mixer that publishes it as a container image on
the libmxl of the MXL domain it runs in. Everything upstream builds still
builds the same way.

## Fork boundaries

- `main` carries the fork. `upstream-main` tracks LeeO86/mxl-audio-mixer
  unmodified and is the base for merging upstream in.
- The fork adds; it does not rewrite upstream logic. Keep each change small
  and in upstream's style, so it can go upstream as a pull request of its own
  and an upstream merge stays mechanical.
- What the fork carries beyond upstream:
  - `Dockerfile.dmf`, the image on `ghcr.io/qvest-digital/go-mxl-runtime`.
    Its build stages are `docker/Dockerfile`'s: an upstream merge that
    changes them changes `Dockerfile.dmf` the same way.
  - `.github/workflows/dmf-image.yml`, which builds, smoke-tests and
    publishes the image with its SBOM and gates it on fixable HIGH and
    CRITICAL CVEs (`.trivyignore.yaml` holds the accepted ones), and the
    release train in `.github/workflows/release-please.yml`.
  - Upstream's `publish.yml` is removed: it pushes under upstream's image
    name. Do not reintroduce it from an upstream merge.
- No Helm chart lives here.

## libmxl

Every reader and writer of one MXL domain links the same libmxl. The mixer
loads `libmxl.so.1` at run time, so the Rust bindings stay at upstream's pin
and the runtime image supplies the domain's library; `GO_MXL_TAG` follows the
libmxl the rest of the domain runs. A libmxl whose C API changes in a way the
bindings at upstream's pin do not match needs the bindings moved with it.

## Building

    docker build -f Dockerfile.dmf -t mxl-audio-mixer .

## Releases

release-please cuts `dmf-v<version>` tags; the `dmf-` prefix keeps them apart
from upstream's `v*` tags. A tag publishes
`ghcr.io/qvest-digital/dmf-mf-mxl-audio-mixer:<version>`.
