# ansible-based image
FROM willhallonline/ansible:2.18-alpine-3.18

# update and install necessary packages
RUN apk update && \
    apk add --no-cache \
    openssh-client \
    ca-certificates \
    bash \
    py3-netaddr \
    curl

# install kubectl
ENV KUBECTL_VERSION=v1.30.0
RUN curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl" && \
    install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && \
    rm kubectl
