# livesync-cli-image

Container build of the official headless CLI shipped inside
[vrtmrz/obsidian-livesync](https://github.com/vrtmrz/obsidian-livesync)
(`src/apps/cli`, upstream publishes no image), published as
`ghcr.io/vol1003-labs/livesync-cli:vX.Y.Z` on tag push.

The workflow checks out the upstream repo at the ref pinned by `UPSTREAM_REF`
in `.github/workflows/release.yml` and builds with the upstream
`src/apps/cli/Dockerfile` as-is (multi-stage, `node:22-slim`, entrypoint
wrapper `livesync-cli`).

## Bump procedure

1. Edit `UPSTREAM_REF` in `.github/workflows/release.yml` (upstream release tag).
2. Commit, then `git tag vX.Y.Z && git push origin main --tags`.
3. First publish only: make the GHCR package public.
