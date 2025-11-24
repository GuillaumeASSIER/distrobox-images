FROM registry.fedoraproject.org/fedora-toolbox:44

# Metadata
LABEL org.opencontainers.image.authors="GuillaumeASSIER"
LABEL org.opencontainers.image.source="https://github.com/GuillaumeASSIER/distrobox-images"

ENV PATH=/home/linuxbrew/.linuxbrew/bin:/home/linuxbrew/.linuxbrew/sbin:${PATH}

# Install tooling in one layer to reduce image size and number of layers.
ADD --chmod=+x https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh /tmp/install-homebrew.sh

RUN set -eux && \
    dnf -y --setopt=install_weak_deps=False --setopt=tsflags=nodocs install \
        git tmux python3-firewall pipx go-task uv docker-cli dnf-plugins-core curl file procps-ng gcc make && \
    NONINTERACTIVE=1 CI=1 /bin/bash /tmp/install-homebrew.sh && \
    echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' > /etc/profile.d/homebrew.sh && \
    . /etc/profile.d/homebrew.sh && \
    brew update && \
    brew install sops deno bun lazygit age opentofu just hugo opencode block-goose || true && \
    dnf clean all && rm -rf /var/cache/dnf /tmp/*
