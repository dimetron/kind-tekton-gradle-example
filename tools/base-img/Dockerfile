FROM gradle
LABEL maintainer="Dmytro Rashko <drashko@me.com>"

ENV TERM=xterm-color

# set environment variables
RUN echo "LANG=en_US.utf-8" >> /etc/environment \
 && echo "LC_ALL=en_US.utf-8" >> /etc/environment

WORKDIR /root

#kubectl
RUN curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl" && \
    mv kubectl /usr/bin/kubectl && \
    chmod +x /usr/bin/kubectl

#tekton
RUN curl -sLO "https://github.com/tektoncd/cli/releases/download/v0.11.0/tkn_0.11.0_Linux_x86_64.tar.gz" && \
    tar xvzf tkn_0.11.0_Linux_x86_64.tar.gz -C /usr/bin tkn && \
    chmod +x /usr/bin/tkn

#kapp
RUN curl -sLO "https://github.com/k14s/kapp/releases/download/v0.33.0/kapp-linux-amd64" && \
    mv kapp-linux-amd64 /usr/bin/kapp && \
    chmod +x /usr/bin/kapp

#jq
RUN apt-get update ; apt-get install --yes --no-install-recommends jq \
    && rm -rf /var/lib/apt/lists/*
