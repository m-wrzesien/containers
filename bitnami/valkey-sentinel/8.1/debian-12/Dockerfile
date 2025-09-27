# Copyright Broadcom, Inc. All Rights Reserved.
# SPDX-License-Identifier: APACHE-2.0

# TODO: build the valkey here instead of pulling image
FROM ghcr.io/valkey-io/valkey:8.1.3-bookworm

ARG DOWNLOADS_URL="downloads.bitnami.com/files/stacksmith"
ARG TARGETARCH

LABEL org.opencontainers.image.documentation="https://github.com/m-wrzesien/containers/tree/main/bitnami/valkey-sentinel/README.md" \
      org.opencontainers.image.source="https://github.com/m-wrzesien/containers/tree/main/bitnami/valkey-sentinel" \
      org.opencontainers.image.title="valkey-sentinel" \
      org.opencontainers.image.version="8.1.3"

ENV HOME="/" \
    OS_ARCH="${TARGETARCH:-amd64}" \
    OS_FLAVOUR="debian-12" \
    OS_NAME="linux" \
    PATH="/opt/bitnami/valkey-sentinel/bin:$PATH"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]
# Install required system packages and dependencies
RUN install_packages ca-certificates curl libssl3 procps
RUN apt-get update && apt-get upgrade -y && \
    apt-get clean && rm -rf /var/lib/apt/lists /var/cache/apt/archives
RUN chmod g+rwX /opt/bitnami
RUN find / -perm /6000 -type f -exec chmod a-s {} \; || true
RUN uninstall_packages curl

COPY rootfs /
RUN /opt/bitnami/scripts/valkey-sentinel/postunpack.sh
ENV APP_VERSION="8.1.3" \
    BITNAMI_APP_NAME="valkey-sentinel" \
    IMAGE_REVISION="5"

EXPOSE 26379

USER 1001
ENTRYPOINT [ "/opt/bitnami/scripts/valkey-sentinel/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/valkey-sentinel/run.sh" ]
