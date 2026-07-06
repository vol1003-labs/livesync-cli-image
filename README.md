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

## Runtime notes (homelab)

- The image's final stage has no `USER`; the homelab pod runs it as uid 1000
  (`runAsUser`/`fsGroup`), which matches the hermes sandbox user `pn` sharing
  the vault PVC.
- The entrypoint wrapper prepends the database path
  (`${LIVESYNC_DB_PATH:-/data}`) to the args, so the pod passes only
  `--vault /vault --settings /secrets/livesync/settings.json` (default
  command = `daemon`).
- gVisor drops inotify → set `CHOKIDAR_USEPOLLING=1` / `CHOKIDAR_INTERVAL=1000`
  (honored by chokidar v4).

Used by: homelab `gitops/apps/hermes/` (sandbox sidecar), replacing
`livesync-bridge`.
