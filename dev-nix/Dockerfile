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

# Copy custom bashrc from filesystem
COPY filesystem/etc/skel/.bashrc /etc/skel/.bashrc

# Install nix packages using nix-env with explicit channel
RUN nix-channel --add https://nixos.org/channels/nixpkgs-unstable nixpkgs && \
    nix-channel --update && \
    NIXPKGS_ALLOW_BROKEN=1 nix-env -i \
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

# Install opencode, rtk, httpx, nuclei, subfinder from binary caches
RUN nix-env -i opencode httpx nuclei subfinder || true

# Install skills.sh via nix-installed bun
RUN /nix/var/nix/profiles/default/bin/bunx skills.sh init --skip-confirm || true

# Set PATH
ENV PATH=/nix/var/nix/profiles/default/bin:/root/.nix-profile/bin:${PATH}
