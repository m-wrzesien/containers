# Copyright Broadcom, Inc. All Rights Reserved.
# SPDX-License-Identifier: APACHE-2.0

# TODO: build the valkey here instead of pulling image
FROM ghcr.io/valkey-io/valkey:8.1.3-bookworm

ARG TARGETARCH

LABEL org.opencontainers.image.documentation="https://github.com/m-wrzesien/containers/tree/main/bitnami/valkey/README.md" \
      org.opencontainers.image.source="https://github.com/m-wrzesien/containers/tree/main/bitnami/valkey" \
      org.opencontainers.image.title="valkey" \
      org.opencontainers.image.version="8.1.3"

ENV HOME="/" \
    OS_ARCH="${TARGETARCH:-amd64}" \
    OS_FLAVOUR="debian-12" \
    OS_NAME="linux"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]
# Install required system packages and dependencies
RUN install_packages ca-certificates curl libgomp1 libssl3 procps

ENV WAIT_FOR_VERSION="v1.0.10" \
    WAIT_FOR_CHECKSUM_AMD64="75c775b98d52cbe3e8ac98dfcecd8fb6a1f6c4abf7d5a2438ff7b0beb0fe84d2" \
    WAIT_FOR_CHECKSUM_ARM64="17e4d8ebba42cd3a1f2b0334d20dac33190982be82298146c79a5378110e9458" \
    WAIT_FOR_ARCHIVE="wait-for-port.tar.gz" \
    WAIT_FOR_BIN="wait-for-port" 

ENV WAIT_FOR_URL="https://github.com/bitnami/${WAIT_FOR_BIN}/releases/download/${WAIT_FOR_VERSION}/${WAIT_FOR_BIN}-${OS_NAME}-${OS_ARCH}.tar.gz"

RUN curl -SsLf "${WAIT_FOR_URL}" -o "${WAIT_FOR_ARCHIVE}" && \
    if [[ "$OS_ARCH" == "amd64" ]]; then \
        echo "${WAIT_FOR_CHECKSUM_AMD64}" "${WAIT_FOR_ARCHIVE}" | sha256sum --check ; \
    else \
        echo "${WAIT_FOR_CHECKSUM_ARM64}" "${WAIT_FOR_ARCHIVE}" | sha256sum --check ; \
    fi && \
    tar -zxf "${WAIT_FOR_ARCHIVE}" -C /opt/bitnami --no-same-owner && \
    mv "/opt/bitnami/${WAIT_FOR_BIN}-${OS_NAME}-${OS_ARCH}" "/opt/bitnami/${WAIT_FOR_BIN}" && \
    rm "${WAIT_FOR_ARCHIVE}"

RUN apt-get update && apt-get upgrade -y && \
    apt-get clean && rm -rf /var/lib/apt/lists /var/cache/apt/archives
RUN chmod g+rwX /opt/bitnami
RUN find / -perm /6000 -type f -exec chmod a-s {} \; || true
RUN uninstall_packages curl

COPY rootfs /
RUN /opt/bitnami/scripts/valkey-cluster/postunpack.sh
ENV APP_VERSION="8.1.3" \
    BITNAMI_APP_NAME="valkey-cluster" \
    IMAGE_REVISION="5" \
    PATH="/opt/bitnami/common/bin:/opt/bitnami/valkey/bin:$PATH"

EXPOSE 6379

USER 1001
ENTRYPOINT [ "/opt/bitnami/scripts/valkey-cluster/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/valkey-cluster/run.sh" ]
