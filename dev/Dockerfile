FROM registry.fedoraproject.org/fedora-toolbox:43

# Upgrading image
RUN dnf update
RUN dnf upgrade

# Tooling
RUN dnf install -y git python3-firewall pipx just hugo opentofu go-task age

# SOPS
RUN dnf install -y https://github.com/getsops/sops/releases/download/v3.7.3/sops-3.7.3-1.x86_64.rpm

# Lazygit
RUN dnf copr enable atim/lazygit -y
RUN dnf install -y lazygit
