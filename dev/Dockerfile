FROM registry.fedoraproject.org/fedora-toolbox:41

# Upgrading image
RUN dnf update
RUN dnf upgrade

# Install tollong
RUN dnf install -y git python3-firewall pipx just hugo opentofu go-task

# Install SOPS
RUN dnf install -y https://github.com/getsops/sops/releases/download/v3.7.3/sops-3.7.3-1.x86_64.rpm
