# build stage
from mcr.microsoft.com/azure-cli as builder

run mkdir -p /apps/k9s && \
    tdnf update -y && \
    tdnf install -y helm \
                   kubernetes-client \
                   git \
                   vim \
                   make \
                   gawk \
                   go \
                   mpfr && \
    tdnf clean all

workdir /apps/k9s

# set up bash completion
run echo "source /etc/bash_completion.d/azure-cli" >> /etc/bash.bashrc && \
    echo "source /etc/bash-completion.d/bash_completion" >> /etc/bash.bashrc && \
    kubectl completion bash | tee /etc/bash_completion.d/kubectl > /dev/null && \
    chmod a+r /etc/bash_completion.d/kubectl

# clone and build k9s
run git clone --depth 1 https://github.com/derailed/k9s.git /apps/k9s && \
    make build && \
    cp /apps/k9s/execs/k9s /bin/k9s && \
    rm -rf /apps/k9s

# production stage
from mcr.microsoft.com/azure-cli

workdir /root

# copy only necessary files from builder stage
copy --from=builder /bin/k9s /bin/k9s
copy --from=builder /etc/bash.bashrc /etc/bash.bashrc
copy --from=builder /usr/share/vim /usr/share/vim
copy --from=builder /usr/lib64/libmpfr.so.6 /usr/lib64/libmpfr.so.6

# set environment variables
env ld_library_path=/usr/lib64:$ld_library_path

cmd ["bash"]