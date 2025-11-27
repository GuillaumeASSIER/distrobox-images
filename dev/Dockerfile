FROM registry.fedoraproject.org/fedora-toolbox:44

# Metadata
LABEL org.opencontainers.image.authors="GuillaumeASSIER"
LABEL org.opencontainers.image.source="https://github.com/GuillaumeASSIER/distrobox-images"

ENV PATH=/home/linuxbrew/.linuxbrew/bin:/home/linuxbrew/.linuxbrew/sbin:${PATH}

# Package lists
ENV DNF_PACKAGES="git tmux python3-firewall pipx go-task uv docker-cli dnf-plugins-core curl file procps-ng gcc make"
ENV DNF_PLAYWRIGHT_PACKAGES="chromium-headless at-spi2-atk"
ENV BREW_DEV_PACKAGES="development-tools gcc deno bun lazygit just hugo"
ENV BREW_OPS_PACKAGES="age sops opentofu terraform"
ENV BREW_AI_PACKAGES="opencode block-goose-cli"

# Base install
RUN dnf install -y ${DNF_PACKAGES} && \
        dnf clean all && rm -rf /var/cache/dnf /tmp/* 

# Playwright dependencies 
RUN dnf install -y ${DNF_PLAYWRIGHT_PACKAGES} &&\
        dnf clean all && rm -rf /var/cache/dnf /tmp/*

RUN useradd -m -s /bin/bash linuxbrew

# Cache the Homebrew install script
ADD --chmod=+x --chown=linuxbrew:linuxbrew  https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh /tmp/install-homebrew.sh

#######################
#      Homebrew section      #
#######################

# Run the installer as the non-root `linuxbrew` user. Create the system-wide
# profile file as root afterwards so other shells can pick up Homebrew.
RUN chown linuxbrew:linuxbrew /tmp/install-homebrew.sh && \
    su - linuxbrew -c "NONINTERACTIVE=1 CI=1 /bin/bash /tmp/install-homebrew.sh" && \
    echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' > /etc/profile.d/homebrew.sh && \
    chmod 644 /etc/profile.d/homebrew.sh

# Brew packages installation (run as linuxbrew)
RUN su - linuxbrew -c "/home/linuxbrew/.linuxbrew/bin/brew update" && \
    su - linuxbrew -c "/home/linuxbrew/.linuxbrew/bin/brew install ${BREW_DEV_PACKAGES} || true" && \
    su - linuxbrew -c "/home/linuxbrew/.linuxbrew/bin/brew install ${BREW_OPS_PACKAGES} || true" && \
    su - linuxbrew -c "/home/linuxbrew/.linuxbrew/bin/brew install ${BREW_AI_PACKAGES} || true"

RUN curl -f https://zed.dev/install.sh | ZED_CHANNEL=preview sh