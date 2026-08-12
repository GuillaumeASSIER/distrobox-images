# AGENTS.md

Guidance for AI agents and humans working in this repository.

## What this repo is

Custom [Distrobox](https://github.com/89luca89/distrobox) container images with
the owner's daily dev tooling. Two images are built, pushed and signed to GHCR
by GitHub Actions:

| Image     | Base                     | Package management          |
| --------- | ------------------------ | --------------------------- |
| `dev`     | `fedora-toolbox` (Fedora) | `dnf` + Homebrew            |
| `dev-nix` | `nixpkgs/nix`            | `nix-env` (nixpkgs-unstable) |

`dev-nix` is meant to mirror `dev`'s tooling, so package lists should stay in
sync between the two images.

## Layout

```
dev/                           Fedora image
  Dockerfile
  filesystem/etc/skel/         shell config copied to the user's home on first entry
    .bashrc
    .config/opencode/opencode.json
dev-nix/                       Nix image
  Dockerfile
  filesystem/etc/skel/.bashrc
dev.ini, dev-nix.ini           distrobox assemble configs (pull+replace, start_now)
.github/workflows/dev-build.yml  CI: build/push/sign/verify both images
renovate.json                  dependency bot (nix + github-actions managers)
README.md, LICENSE (MIT)
```

## Non-negotiable conventions

- **Fedora base tag**: set via `ARG FEDORA_TOOLBOX_TAG` at the top of
  `dev/Dockerfile`. Do not hardcode it in the `FROM` line. Check available
  tags at `https://registry.fedoraproject.org/v2/fedora-toolbox/tags/list`
  before bumping.
- **Nix installs MUST target the system profile**: always use
  `nix-env --profile /nix/var/nix/profiles/default -i ...`. Installing into
  the default user profile (`/root/.nix-profile`) makes every package
  invisible at runtime — the distrobox container runs as the non-root host
  user, who cannot read `/root`.
- **`nix-env` is atomic**: if one package fails, none are installed. Keep
  `|| echo "WARNING: ..."` (non-fatal) rather than bare `|| true` or hard
  failures, so the build logs the cause.
- **`skills.sh init` must run via `bunx`** (bun is installed into the system
  profile; `/nix/var/nix/profiles/default/bin/bunx` does not exist at
  build time). Keep it non-fatal.
- Keep `HOMEBREW_NO_AUTO_UPDATE` / `HOMEBREW_NO_INSTALL_CLEANUP` /
  `HOMEBREW_NO_ANALYTICS` and `DNF_FLAGS="--setopt=install_weak_deps=False"`:
  they keep builds fast and images lean.
- **Action versions in the workflow are Renovate-managed** (pinned SHAs with
  `# vX.Y.Z` comments). Do not bump them manually — update the comment if you
  must change the pin.

## Common commands

```bash
# Create/replace the containers from the latest published images
distrobox assemble create --file dev.ini
distrobox assemble create --file dev-nix.ini

# Local build (from repo root)
docker buildx build --build-arg BUILDKIT_INLINE_CACHE=1 dev/ -t dev:local
docker buildx build --build-arg BUILDKIT_INLINE_CACHE=1 dev-nix/ -t dev-nix:local
```

## CI / CD

- `.github/workflows/dev-build.yml` runs one matrix job (`dev`, `dev-nix`):
  build with Buildx + GHA cache, push to
  `ghcr.io/guillaumeassier/distrobox-images/<image>`, then sign and **verify**
  with Cosign (keyless, GitHub OIDC).
- Triggers: daily cron (18:39 UTC), push/tag/PR on `main`, manual
  `workflow_dispatch`. Builds never push on PRs.
- `concurrency` serializes runs per ref — don't remove it, two builds racing
  on the same tags is a footgun.

## Renovate

- `renovate.json`: `nix` manager enabled (bumps the `nix-env` packages in
  `dev-nix/Dockerfile`), grouped into a single `nix-dependencies` PR;
  `github-actions` grouped, digest-pinned, auto-merged on a 2-week schedule.
- Renovate compares against the moving `nixpkgs-unstable` channel; the channel
  is deliberately not pinned (reproducible pinning would be a deliberate,
  separate change).
