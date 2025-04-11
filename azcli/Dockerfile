#buildstage
FROM mcr.microsoft.com/azure-cli AS builder

#create tempdir
RUN mkdir /apps
WORKDIR /apps

#install packages
RUN tdnf update -y && \
  tdnf install -y helm \
  kubernetes-client \
  git \
  vim \
  make \
  gawk \
  go && \ 
  tdnf clean all

#add az completion
RUN echo "source /etc/bash_completion.d/azure-cli" >> /etc/bash.bashrc
RUN echo  "source /etc/bash-completion.d/bash_completion"
RUN kubectl completion bash | tee /etc/bash_completion.d/kubectl > /dev/null && chmod a+r /etc/bash_completion.d/kubectl

#clone k9s repo
RUN git clone https://github.com/derailed/k9s.git
WORKDIR /apps/k9s 

#make k9s and copy binary
RUN make build && cp /apps/k9s/execs/k9s /bin/k9s && rm -r /apps/k9s
WORKDIR /root

#prod stage
FROM mcr.microsoft.com/azure-cli

#copy binaries from builder
COPY --from=builder /bin /bin
COPY --from=builder /usr/bin /usr/bin

#copy bashrc from builder
COPY --from=builder /etc/bash.bashrc /etc/bash.bashrc

# copy vim stuff from builder
COPY --from=builder /etc/vimrc /etc/vimrc
COPY --from=builder /usr/share/vim /usr/share/vim
