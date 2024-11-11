FROM registry.fedoraproject.org/fedora-toolbox:42

# Upgrading image
RUN dnf update
RUN dnf upgrade

# Install tollong
RUN dnf install git python3-firewall pipx just hugo opentofu go-task

# Install SOPS
RUN dnf install https://github.com/getsops/sops/releases/download/v3.7.3/sops-3.7.3-1.x86_64.rpm
