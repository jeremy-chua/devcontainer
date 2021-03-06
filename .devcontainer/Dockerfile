# Note: You can use any Debian/Ubuntu based image you want. 
# FROM mcr.microsoft.com/vscode/devcontainers/base:buster

# [Choice] Go version: 1, 1.16, 1.17
ARG GO_VARIANT=1
FROM mcr.microsoft.com/vscode/devcontainers/go:0-${GO_VARIANT}

# [Option] Install zsh
ARG INSTALL_ZSH="true"
# [Option] Upgrade OS packages to their latest versions
ARG UPGRADE_PACKAGES="true"
# [Option] Enable non-root Docker access in container
ARG ENABLE_NONROOT_DOCKER="true"
# [Option] Use the OSS Moby CLI instead of the licensed Docker CLI
ARG USE_MOBY="true"

# Install needed packages and setup non-root user. Use a separate RUN statement to add your
# own dependencies. A user of "automatic" attempts to reuse an user ID if one already exists.
ARG USERNAME=automatic
ARG USER_UID=1000
ARG USER_GID=$USER_UID
COPY library-scripts/*.sh /tmp/library-scripts/
RUN apt-get update -y \
    && /bin/bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" "true" "true" \
    # Use Docker script from script library to set things up
    && /bin/bash /tmp/library-scripts/docker-debian.sh "${ENABLE_NONROOT_DOCKER}" "/var/run/docker-host.sock" "/var/run/docker.sock" "${USERNAME}" \
    # Clean up
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts/


# [Choice] Node.js version: none, lts/*, 16, 14, 12, 10
ARG NODE_VERSION="none"
RUN if [ "${NODE_VERSION}" != "none" ]; then su $USERNAME -c "umask 0002 && . /usr/local/share/nvm/nvm.sh && nvm install ${NODE_VERSION} 2>&1"; fi


# # Bitrise CLI
ARG BITRISE_CLI_VERSION=1.47.2
RUN curl -fL https://github.com/bitrise-io/bitrise/releases/download/${BITRISE_CLI_VERSION}/bitrise-$(uname -s)-$(uname -m) > /usr/local/bin/bitrise \ 
    && chmod +x /usr/local/bin/bitrise \
    && bitrise update && \ 
    bitrise setup --full


# Flutter
RUN git clone -b stable https://github.com/flutter/flutter.git /flutter
RUN chown -R $USERNAME:root /flutter
ENV PATH="/flutter/bin:${PATH}"
# Download Dart SDk from Flutter
RUN flutter --version
RUN chown -R $USERNAME:root /flutter

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    # Clean up
    && apt-get autoremove -y && apt-get clean -y

# [Optional] Uncomment this section to install additional OS packages.
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends xz-utils zip unzip libglu1-mesa

# Setting the ENTRYPOINT to docker-init.sh will configure non-root access 
# to the Docker socket. The script will also execute CMD as needed.
ENTRYPOINT [ "/usr/local/share/docker-init.sh" ]
CMD [ "sleep", "infinity" ]
