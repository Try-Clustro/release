# Clustro releases

Binaries of `clustroctl`, the single binary that runs Kubernetes Pod YAML on clusters of up to 22 nodes. Source and docs: [getclustro.in](https://getclustro.in).

## Install

```sh
curl -sfL https://getclustro.in/install.sh | sh
```

The installer picks your OS and CPU (Linux or macOS; amd64 or arm64), downloads the binary from this repo's [Releases](https://github.com/Try-Clustro/release/releases), checks its SHA-256 against `SHA256SUMS`, and installs it to `/usr/local/bin`. Pin a version with `CLUSTRO_VERSION=v2.0.0-alpha.1`.

## Layout

| Path | What |
|---|---|
| `latest` | The current version |
| `<version>/` | `clustroctl-{linux,darwin}-{amd64,arm64}`, `clustroctl-darwin-universal`, `SHA256SUMS`, `VERSION`, `RELEASE_NOTES.md` |
| `CHANGELOG.md` | Every release, newest first |

Each push to `main` publishes the version in `latest` ([workflow](.github/workflows/release.yml)):
- tag and Release `<version>` with the files above;
- tag and Release `latest`, moved to the same commit with the same files. `install.sh` reads `releases/download/latest/VERSION`.

A push that doesn't change the version publishes nothing.

## Making a release

From the Clustro source repo: `v2/release.sh [--notes notes.md]`. It builds every target, fills `<version>/`, updates `latest` and `CHANGELOG.md`, and prints the `git add / commit / push` to run. Versions only go forward; a published version is never replaced.
