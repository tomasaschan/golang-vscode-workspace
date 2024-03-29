#-------------------------------------------------------------------------------------------------------------
# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License. See https://go.microsoft.com/fwlink/?linkid=2090316 for license information.
# https://github.com/microsoft/vscode-remote-try-go/blob/master/.devcontainer/Dockerfile
#-------------------------------------------------------------------------------------------------------------

ARG TIMEZONE='Europe/Stockholm'

FROM golang:latest

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# Or your actual UID, GID on Linux if not the default 1000
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Configure apt, install packages and tools
RUN apt-get update \
  && apt-get -y install --no-install-recommends apt-utils dialog 2>&1 \
  #
  # Verify git, process tools, lsb-release (common in install instructions for CLIs) installed
  && apt-get -y install git procps lsb-release locales \
  #
  # Install sudo (config further down)
  && apt-get install -y sudo \
  #
  # Install Docker and Docker Compose
  && apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common \
  && curl -fsSL "https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]')/gpg" | apt-key add - 2>/dev/null \
  && add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]') $(lsb_release -cs) stable" \
  && apt-get update \
  && apt-get install -y docker-ce-cli \
  && curl -sSL "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose \
  && chmod +x /usr/local/bin/docker-compose \
  #
  # Install gocode-gomod
  && go get -x -d github.com/stamblerre/gocode 2>&1 \
  && go build -o gocode-gomod github.com/stamblerre/gocode \
  && mv gocode-gomod $GOPATH/bin/ \
  #
  # Install Go tools
  && go get -u -v \
  github.com/mdempsky/gocode \
  github.com/uudashr/gopkgs/cmd/gopkgs \
  github.com/ramya-rao-a/go-outline \
  github.com/acroca/go-symbols \
  github.com/godoctor/godoctor \
  golang.org/x/tools/cmd/guru \
  golang.org/x/tools/cmd/gorename \
  github.com/rogpeppe/godef \
  github.com/zmb3/gogetdoc \
  github.com/haya14busa/goplay/cmd/goplay \
  github.com/sqs/goreturns \
  github.com/josharian/impl \
  github.com/davidrjenni/reftools/cmd/fillstruct \
  github.com/fatih/gomodifytags \
  github.com/cweill/gotests/... \
  golang.org/x/tools/cmd/goimports \
  golang.org/x/lint/golint \
  golang.org/x/tools/cmd/gopls \
  github.com/alecthomas/gometalinter \
  honnef.co/go/tools/... \
  github.com/golangci/golangci-lint/cmd/golangci-lint \
  github.com/mgechev/revive \
  github.com/derekparker/delve/cmd/dlv 2>&1 \
  #
  # Clean up
  && apt-get autoremove -y \
  && apt-get clean -y \
  && rm -rf /var/lib/apt/lists/*

# Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
RUN  groupadd --gid $USER_GID $USERNAME \
  && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
  && mkdir -p /home/$USERNAME/.vscode-server/data /home/$USERNAME/.vscode-server/bin \
  && chown -R ${USER_UID}:${USER_GID} /home/$USERNAME/.vscode-server \
  # Add sudo support
  && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
  && chmod 0440 /etc/sudoers.d/$USERNAME

# Source profile scripts from ~/.bashrc.d
RUN mkdir -p /home/vscode/.bashrc.d && echo "\n\nif [ -d ~/.bashrc.d ]; then\n  for rc in ~/.bashrc.d/*; do\n    . \$rc\n  done\nfi" >> /home/vscode/.bashrc

# Add gitconfig
ADD .gitconfig /home/vscode/
# Add scripts to configure and connect to ssh-agent on startup
ADD 00-config-ssh-keys.sh /home/vscode/.bashrc.d/
ADD 01-config-ssh-agent.sh /home/vscode/.bashrc.d/

# Configure locales
RUN echo ${TIMEZONE} > /etc/timezone \
  && dpkg-reconfigure --frontend=noninteractive tzdata \
  && sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen \
  && echo 'LANG="en_US.UTF-8"' > /etc/default/locale \
  && dpkg-reconfigure --frontend=noninteractive locales \ 
  && update-locale LANG=en_US.UTF-8
