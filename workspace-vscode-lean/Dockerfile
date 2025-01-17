# Image based on
# https://github.com/PrairieLearn/PrairieLearn/blob/master/workspaces/vscode-python/Dockerfile

# noble is the codename for Ubuntu 24.04 LTS
FROM codercom/code-server:4.96.2-noble

# On PL, we want to standardize on using 1001:1001 for the user. We change the
# "coder" account's UID and GID here so that fixuid has no work to do later.
# This speeds up container loading time drastically and avoids timeouts.
# With Ubuntu-derived base images, this may be a no-op, because recent Ubuntu
# images have a default "ubuntu" user 1000:1000, and in that case, the
# "coder" user may already be configured as 1001:1001.
USER root
RUN OLD_UID=$(id -u coder) && \
    OLD_GID=$(id -g coder) && \
    NEW_UID=1001 && \
    NEW_GID=1001 && \
    groupmod -g $NEW_GID coder && \
    usermod -u $NEW_UID -g $NEW_GID coder && \
    find /home -user $OLD_UID -execdir chown -h $NEW_UID {} + && \
    find /home -group $OLD_GID -execdir chgrp -h $NEW_GID {} +

# Remove sudo permissions for default user, then test
USER root
RUN find /etc/sudoers.d -type f -not -name README -delete && \
    sudo -u coder bash -c 'if sudo echo "Test failed: User can sudo" ; then false ; fi'

# Apt installs: Enable manpages with "unminimize", and then install common utilities.
USER root
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && \
    apt-get install -y man-db unminimize && \
    yes | unminimize && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    find /tmp -not -path /tmp -delete
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    gosu curl git htop less nano unzip vim wget zip && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    find /tmp -not -path /tmp -delete

# Make sure to build using bash as the shell so that conda/mamba hooks will
# work during installation.
SHELL ["/bin/bash", "-c"]

# Install lean
USER coder
ENV HOME=/home/coder
RUN curl https://raw.githubusercontent.com/leanprover/elan/master/elan-init.sh -sSf | sh -s -- -y --default-toolchain leanprover/lean4:4.15.0

# Install VS Code extensions and clear the extension cache to reduce image size.
USER coder
RUN code-server --disable-telemetry --force \
    # vscode support for lean
    --install-extension leanprover.lean4 \
    # vscodevim is disabled in settings.json
    --install-extension vscodevim.vim \
    && rm -rf /home/coder/.local/share/code-server/CachedExtensionVSIXs

# We standardize on /home/coder/workspace as the default working directory
# for the editor, and we recommend that staff set this as workspaceOptions
# "home" in the question configuration. See the README.md for details.
USER coder
RUN mkdir -p "/home/coder/workspace" "/home/coder/.local/share/code-server/User"
WORKDIR "/home/coder/workspace"
COPY --chmod=0644 --chown=coder:coder settings.json /home/coder/.local/share/code-server/User/

# Prepare the entrypoint helper that steps down to a limited user in local dev mode
COPY --chmod=0755 --chown=root:root pl-gosu-helper.sh /usr/bin/

# Make sure that code-server's entrypoint doesn't run fixuid unnecessarily
USER root
RUN mkdir -p /run /var/run && \
    touch /run/fixuid.ran /var/run/fixuid.ran

USER coder
EXPOSE 8080
ENTRYPOINT ["/usr/bin/pl-gosu-helper.sh", "/usr/bin/entrypoint.sh", "--auth", "none", \
    "--disable-update-check", "--disable-telemetry", \
    "--bind-addr", "0.0.0.0:8080", "."]
