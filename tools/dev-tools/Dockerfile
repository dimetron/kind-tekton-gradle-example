FROM registry.fedoraproject.org/fedora-minimal:31-x86_64
LABEL maintainer="Dmytro Rashko <drashko@me.com>"

ENV SDKMAN_DIR=/root/.sdkman
ENV TERM=xterm-color

# set environment variables
RUN echo "LANG=en_US.utf-8" >> /etc/environment \
 && echo "LC_ALL=en_US.utf-8" >> /etc/environment

WORKDIR /root

COPY docker-ce.repo /etc/yum.repos.d/docker-ce.repo

#Install cli tools    
RUN echo "Installing additional software" \ 
    && ls /usr/bin \
    && microdnf -y install --enablerepo=docker-ce-stable yum-utils device-mapper-persistent-data lvm2 sudo \
                docker-ce-cli containerd.io which wget zip unzip jq tar passwd openssh openssh-server squid conntrack-tools torsocks iptables \
                bash sshpass hostname curl ca-certificates libstdc++ ca-certificates bash git zip unzip sed vim-enhanced \
                python37 pip sshuttle openssl bash zsh procps rsync mc htop openssh skopeo ansible findutils jq bzip2 shadow-utils iptraf httpie \
    && rpm -ivh https://packagecloud.io/datawireio/telepresence/packages/fedora/31/telepresence-0.105-1.x86_64.rpm/download.rpm --nodeps \
    && pip install --upgrade pip \
    && microdnf -y clean all \
    && rm -rf /var/lib/{cache,log} /var/log/lastlog /opt/couchbase/samples /usr/bin/dockerd-ce /usr/bin/containerd \
    && mkdir /var/log/lastlog

RUN curl -sLo ./kind "https://github.com/kubernetes-sigs/kind/releases/download/v0.8.1/kind-$(uname)-amd64" \
    && mv kind  /usr/bin \
    && chmod +x /usr/bin/kind

#install krew for kubectl
RUN curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/krew.{tar.gz,yaml}" \
    && tar zxvf krew.tar.gz ; cat krew.yaml \
    && ./krew-linux_amd64 install --manifest=krew.yaml --archive=krew.tar.gz \
    && ./krew-linux_amd64 update \
    && cp ./krew-linux_amd64 /usr/bin/krew && chmod +x /usr/bin/krew \
    && rm -rf krew*

#install ZSH custom theme
RUN sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"

RUN env && bash --version && \
    curl -s "https://get.sdkman.io" | /bin/bash && \
    echo "sdkman_auto_answer=true" > $SDKMAN_DIR/etc/config && \
    echo "sdkman_auto_selfupdate=false" >> $SDKMAN_DIR/etc/config && \
    echo "sdkman_insecure_ssl=true" >> $SDKMAN_DIR/etc/config

#install maven and java 8
RUN echo "Install JAVA GRADLE" \
    && zsh -c 'set +x;source /root/.sdkman/bin/sdkman-init.sh' \
    && zsh -c 'source "/root/.sdkman/bin/sdkman-init.sh" && sdk install gradle' \
    && zsh -c 'source "/root/.sdkman/bin/sdkman-init.sh" && sdk ls java && sdk install java' \
    && rm -rf /root/.sdkman/archives

ENV PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/root/.sdkman/bin:/root/.sdkman/candidates/gradle/current/bin:/root/.krew/bin

#use openssh
RUN echo "Setup SSH server defaults" \
  && sed -i s/#PermitRootLogin.*/PermitRootLogin\ yes/ /etc/ssh/sshd_config  \
  && sed -i s/#PermitTunnel.*/PermitTunnel\ yes/ /etc/ssh/sshd_config  \
  && sed -i s/#AllowTcpForwarding.*/AllowTcpForwarding\ yes/ /etc/ssh/sshd_config  \
  && cat /etc/ssh/sshd_config

#kubectl
RUN curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl" && \
    mv kubectl /usr/bin/kubectl && \
    chmod +x /usr/bin/kubectl

#k9s
RUN curl -sL "https://github.com/derailed/k9s/releases/download/v0.20.5/k9s_Linux_x86_64.tar.gz" | tar xvz && \
    mv k9s /usr/bin

#ctop
RUN curl -sLO "https://github.com/bcicen/ctop/releases/download/v0.7.1/ctop-0.7.1-linux-amd64" && \
    mv ctop-0.7.1-linux-amd64 /usr/bin/ctop && \
    chmod +x /usr/bin/ctop

#tekton
RUN curl -sLO "https://github.com/tektoncd/cli/releases/download/v0.11.0/tkn_0.11.0_Linux_x86_64.tar.gz" && \
    tar xvzf tkn_0.11.0_Linux_x86_64.tar.gz -C /usr/bin tkn && \
    chmod +x /usr/bin/tkn

#kapp
RUN curl -sLO "https://github.com/k14s/kapp/releases/download/v0.33.0/kapp-linux-amd64" && \
    mv kapp-linux-amd64 /usr/bin/kapp && \
    chmod +x /usr/bin/kapp

#kail
RUN curl -sLO "https://github.com/boz/kail/releases/download/v0.15.0/kail_0.15.0_linux_amd64.tar.gz" && \
    tar xvzf kail_0.15.0_linux_amd64.tar.gz  && \
    mv kail /usr/bin      && \
    chmod +x /usr/bin/kail

#add custom user
ADD https://github.com/dimetron.keys /root/.ssh/authorized_keys

RUN echo "Verify versions"  \
    && tkn version          \
    && echo "Create default ssh keys "  \
    && passwd -d root 2>/dev/null       \
    && ssh-keygen -A                    \
    && echo "alias vi=vim"    >> .zshrc \
    && echo "alias k=kubectl" >> .zshrc
    
CMD tail -f /dev/null

#ENTRYPOINT ["/entrypoint.sh"]