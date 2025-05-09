# builder stage
FROM alpine:3.19 AS builder

WORKDIR /tmp

# install build dependencies
RUN apk add --update --virtual .build-deps --no-cache \
    gnupg \
    curl \
    unzip \
    gcc \
    musl-dev \
    libffi-dev \
    openssl-dev \
    make \
    python3-dev

# install python and pip
RUN apk add --no-cache \
    python3 \
    py3-pip

# create virtual environment and install ansible
ENV VENV_PATH=/opt/venv
RUN python3 -m venv $VENV_PATH && \
    $VENV_PATH/bin/pip install --upgrade pip && \
    $VENV_PATH/bin/pip install --no-cache-dir ansible

# download and verify terraform 1.9.5
ENV TERRAFORM_VERSION=1.9.5

RUN wget https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip && \
    wget https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_SHA256SUMS && \
    wget https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_SHA256SUMS.sig && \
    wget -qO- https://www.hashicorp.com/.well-known/pgp-key.txt | gpg --import && \
    gpg --verify terraform_${TERRAFORM_VERSION}_SHA256SUMS.sig terraform_${TERRAFORM_VERSION}_SHA256SUMS && \
    grep terraform_${TERRAFORM_VERSION}_linux_amd64.zip terraform_${TERRAFORM_VERSION}_SHA256SUMS | sha256sum -c && \
    unzip terraform_${TERRAFORM_VERSION}_linux_amd64.zip && \
    chmod +x terraform

# final production stage
FROM alpine:3.19

WORKDIR /root

# install runtime dependencies including git, kubectl, helm
RUN apk update && apk add --no-cache \
    openssh-client \
    ca-certificates \
    bash \
    curl \
    jq \
    kubectl \
    helm \
    git \
    python3 \
    nodejs \
    npm

# copy terraform binary
COPY --from=builder /tmp/terraform /usr/local/bin/terraform

# copy python virtual environment with ansible
COPY --from=builder /opt/venv /opt/venv

# set environment path
ENV PATH="/opt/venv/bin:$PATH"

# entrypoint script to activate venv
COPY <<EOF /entrypoint.sh
#!/bin/sh
export PATH="/opt/venv/bin:\$PATH"
exec "\$@"
EOF

RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
CMD ["bash"]
