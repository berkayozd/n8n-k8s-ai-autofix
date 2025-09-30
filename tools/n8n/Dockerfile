FROM alpine:3.20 AS tools
ARG KUBECTL_VER=v1.32.2
ARG HELM_VER=v3.15.3
RUN apk add --no-cache curl tar ca-certificates && \
    curl -L -o /tmp/kubectl https://dl.k8s.io/release/${KUBECTL_VER}/bin/linux/amd64/kubectl && \
    install -m 0755 /tmp/kubectl /usr/local/bin/kubectl && \
    curl -L https://get.helm.sh/helm-${HELM_VER}-linux-amd64.tar.gz | tar xz -C /tmp && \
    install -m 0755 /tmp/linux-amd64/helm /usr/local/bin/helm

FROM n8nio/n8n:latest
USER root
COPY --from=tools /usr/local/bin/kubectl /usr/local/bin/kubectl
COPY --from=tools /usr/local/bin/helm /usr/local/bin/helm
USER node

USER root
RUN set -eux; \
    if command -v apk >/dev/null 2>&1; then \
        apk add --no-cache curl ca-certificates; \
    else \
        apt-get update && apt-get install -y --no-install-recommends curl ca-certificates && \
        rm -rf /var/lib/apt/lists/*; \
    fi; \
    KVER="$(curl -L -s https://dl.k8s.io/release/stable.txt)"; \
    curl -L -o /usr/local/bin/kubectl "https://dl.k8s.io/${KVER}/bin/linux/amd64/kubectl"; \
    chmod +x /usr/local/bin/kubectl

USER node
