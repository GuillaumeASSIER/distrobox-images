FROM registry.fedoraproject.org/fedora-toolbox:44

# Metadata
LABEL org.opencontainers.image.authors="GuillaumeASSIER"
LABEL org.opencontainers.image.source="https://github.com/GuillaumeASSIER/distrobox-images"

# Make versions easy to bump
ARG SOPS_VERSION=3.11.0
ENV BUN_INSTALL=/root/.bun
ENV PATH=${BUN_INSTALL}/bin:${PATH}

# Install tooling in one layer to reduce image size and number of layers.
RUN set -eux; \
    dnf -y --setopt=install_weak_deps=False --setopt=tsflags=nodocs install \
        git python3-firewall pipx just hugo opentofu go-task age uv docker-cli dnf-plugins-core; \
    dnf -y copr enable atim/lazygit -y; \
    dnf -y install lazygit; \
    curl -fsSL -o /tmp/sops.rpm "https://github.com/getsops/sops/releases/download/v${SOPS_VERSION}/sops-${SOPS_VERSION}-1.x86_64.rpm"; \
    dnf -y install /tmp/sops.rpm; rm -f /tmp/sops.rpm; \
    curl -fsSL https://bun.com/install | bash; \
    if [ -x "${BUN_INSTALL}/bin/bun" ]; then "${BUN_INSTALL}/bin/bun" add -g opencode-ai || true; fi; \
    curl -fsSL https://deno.land/install.sh | sh; \
    dnf clean all; rm -rf /var/cache/dnf /tmp/*
