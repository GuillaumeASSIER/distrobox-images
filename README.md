# Distrobox images

Custom [Distrobox](https://github.com/89luca89/distrobox) container images with
my daily tooling, built daily on GitHub Actions and published to the GitHub
Container Registry.

## Images

| Image     | Base                                                              | Contents |
| --------- | ----------------------------------------------------------------- | -------- |
| `dev`     | [fedora-toolbox](https://registry.fedoraproject.org/) (Fedora)    | Homebrew tooling (deno, bun, lazygit, just, hugo…), Docker/Compose, Go, Python/uv, k8s & IaC CLIs (kubectl, helm, k9s, talosctl, opentofu, terragrunt…), security tools (httpx, nuclei, subfinder), Zed, opencode, skills.sh |
| `dev-nix` | [nixpkgs/nix](https://hub.docker.com/r/nixpkgs/nix)               | Same tooling installed from nixpkgs via `nix-env` |

## Quick start

Requires [Distrobox](https://distrobox.it/) with Podman or Docker.

```bash
distrobox assemble create --file dev.ini
# or, for the Nix edition
distrobox assemble create --file dev-nix.ini
```

Both configs pull the latest published image and replace the existing
container on each run (`pull=true`, `replace=true`).

## Customization

- Edit the package lists at the top of `dev/Dockerfile`, or the `nix-env`
  blocks in `dev-nix/Dockerfile`, then push to `main` — or trigger the
  **Dev build** workflow manually from the Actions tab.
- Personal shell tweaks live in `dev/filesystem/etc/skel/` (and
  `dev-nix/filesystem/etc/skel/`): files in `etc/skel` are copied to the
  container user's home on first entry.
- To bump the Fedora toolbox base, change `FEDORA_TOOLBOX_TAG` at the top of
  `dev/Dockerfile` (available tags are listed in the
  [Fedora registry](https://registry.fedoraproject.org/v2/fedora-toolbox/tags/list)).

## CI

`.github/workflows/dev-build.yml` builds and pushes both images to
`ghcr.io/guillaumeassier/distrobox-images/{dev,dev-nix}`:

- daily at 18:39 UTC
- on every push, tag or PR touching `main`
- on manual `workflow_dispatch`

Published images are signed with [Cosign](https://github.com/sigstore/cosign)
(keyless, GitHub OIDC) and the signature is verified right after signing.

## License

[MIT](LICENSE)
