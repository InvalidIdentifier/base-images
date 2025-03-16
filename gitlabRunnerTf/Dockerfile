# builder stage
from alpine:latest as builder

workdir /tmp

# install build dependencies
run apk add --update --virtual .build-deps --no-cache \
    gnupg \
    curl \
    unzip \
    gcc \
    musl-dev \
    libffi-dev \
    openssl-dev \
    make \
    python3-dev

# install python and ansible
run apk add --no-cache \
    python3 \
    py3-pip

run pip3 install --upgrade pip --break-system-packages && \
    pip3 install --no-cache-dir ansible --break-system-packages

# download and verify terraform
run wget https://releases.hashicorp.com/terraform/1.9.2/terraform_1.9.2_linux_amd64.zip && \
    wget https://releases.hashicorp.com/terraform/1.9.2/terraform_1.9.2_SHA256SUMS && \
    wget https://releases.hashicorp.com/terraform/1.9.2/terraform_1.9.2_SHA256SUMS.sig && \
    wget -qO- https://www.hashicorp.com/.well-known/pgp-key.txt | gpg --import && \
    gpg --verify terraform_1.9.2_SHA256SUMS.sig terraform_1.9.2_SHA256SUMS && \
    grep terraform_1.9.2_linux_amd64.zip terraform_1.9.2_SHA256SUMS | sha256sum -c && \
    unzip terraform_1.9.2_linux_amd64.zip && \
    chmod +x terraform

# final production stage
from alpine:latest

workdir /root

# install runtime dependencies
run apk add --no-cache \
    openssh-client \
    bash \
    ca-certificates

# copy terraform and ansible from builder stage
copy --from=builder /tmp/terraform /usr/local/bin/terraform
copy --from=builder /usr/lib/python3.*/site-packages/ /usr/lib/python3.*/site-packages/

# cleanup
run rm -rf /var/cache/apk/* /tmp/*

cmd ["sh"]