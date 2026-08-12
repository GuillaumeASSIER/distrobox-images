FROM nixpkgs/nix

# Metadata
LABEL org.opencontainers.image.authors="GuillaumeASSIER"
LABEL org.opencontainers.image.source="https://github.com/GuillaumeASSIER/distrobox-images"

# BuildKit inline cache
ARG BUILDKIT_INLINE_CACHE=1

# Enable flakes and nix-command
RUN mkdir -p /etc/nix && \
    echo "experimental-features = nix-command flakes" > /etc/nix/nix.conf && \
    echo "sandbox = false" >> /etc/nix/nix.conf

# Set PATH early so nix-env installs in the system profile take effect
# during the build and at runtime.
ENV PATH=/nix/var/nix/profiles/default/bin:${PATH}

# Copy custom bashrc from filesystem
COPY filesystem/etc/skel/.bashrc /etc/skel/.bashrc

# Install nix packages using nix-env with explicit channel.
# Packages are installed into the *system* profile (/nix/var/nix/profiles/default)
# so they are visible to the non-root user at runtime — the default distrobox
# user profile (~/.nix-profile) lives in /root and is unreachable for that user.
RUN nix-channel --add https://nixos.org/channels/nixpkgs-unstable nixpkgs && \
    nix-channel --update && \
    NIXPKGS_ALLOW_BROKEN=1 nix-env --profile /nix/var/nix/profiles/default -i \
    git \
    tmux \
    python3 \
    go-task \
    uv \
    docker \
    docker-compose \
    curl \
    file \
    procps \
    gcc \
    gnumake \
    go \
    jq \
    wget \
    unzip \
    ripgrep \
    fzf \
    eza \
    bat \
    zoxide \
    delta \
    tldr \
    lazygit \
    just \
    hugo \
    deno \
    bun \
    age \
    sops \
    opentofu \
    terragrunt \
    kustomize \
    kubernetes \
    k9s \
    cilium-cli \
    talosctl \
    ruff

# Install opencode, httpx, nuclei, subfinder from binary caches.
# nix-env is atomic: if any package fails, none get installed, so log a
# warning instead of failing the build silently.
RUN nix-env --profile /nix/var/nix/profiles/default -i opencode httpx nuclei subfinder \
    || echo "WARNING: one of opencode/httpx/nuclei/subfinder failed to install (non-fatal)"

# Install skills.sh via bun (installed in the system profile above)
RUN bunx skills.sh init --skip-confirm \
    || echo "WARNING: skills.sh init failed (non-fatal)"
