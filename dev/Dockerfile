FROM registry.fedoraproject.org/fedora-toolbox:44

# Metadata
LABEL org.opencontainers.image.authors="GuillaumeASSIER"
LABEL org.opencontainers.image.source="https://github.com/GuillaumeASSIER/distrobox-images"

ENV PATH=/home/linuxbrew/.linuxbrew/bin:/home/linuxbrew/.linuxbrew/sbin:${PATH}

# Package lists
ENV DNF_PACKAGES="git tmux python3-firewall pipx go-task uv docker-cli dnf-plugins-core curl file procps-ng gcc make"
ENV DNF_PLAYWRIGHT_PACKAGES="chromium-headless at-spi2-atk"
ENV BREW_PACKAGES="age sops deno bun lazygit opentofu terraform just hugo opencode block-goose-cli"

ADD --chmod=+x https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh /tmp/install-homebrew.sh

# Base install
RUN set -eux && \
    dnf -y --setopt=install_weak_deps=False --setopt=tsflags=nodocs install ${DNF_PACKAGES} && \
        dnf clean all && rm -rf /var/cache/dnf /tmp/*

# Playwright dependencies 
RUN dnf install -y ${DNF_PLAYWRIGHT_PACKAGES} &&\
        dnf clean all && rm -rf /var/cache/dnf /tmp/*

# Brew install
RUN NONINTERACTIVE=1 CI=1 /bin/bash /tmp/install-homebrew.sh && \
        echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' > /etc/profile.d/homebrew.sh && \
        . /etc/profile.d/homebrew.sh && \
        brew update && \
        brew install ${BREW_PACKAGES} || true 