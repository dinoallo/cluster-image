ARG goVersion=1.20.4

# FROM node:18 AS buildhtml
# ARG ARCH
# WORKDIR /root
# COPY html .
# RUN yarn install
# RUN yarn run build

FROM golang:$goVersion
LABEL org.opencontainers.image.authors="labring"

USER root
ENV HOME /root
ARG kubeVersion=1.25.6
ARG ttydVersion=1.7.3
ARG helmVersion=3.12.0

ARG ARCH

WORKDIR /root
COPY ./inline.html ./index.html
COPY vim/ .
COPY scripts/start-terminal.sh /usr/bin/
COPY scripts/ttyd-kubectl.sh /usr/bin/
# install pagkages
RUN arch                                                                         && \
    wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | apt-key add - && \
    echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | tee /etc/apt/sources.list.d/mongodb-org-6.0.list && \
    wget -qO - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - &&\
    sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt bullseye-pgdg main" > /etc/apt/sources.list.d/pgdg.list' &&\
    curl -fsSL https://packages.redis.io/gpg | gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb bullseye main" | tee /etc/apt/sources.list.d/redis.list && \
    apt-get update                                                               && \
    apt-get install -y mongodb-mongosh                                           && \
    apt-get install -y default-mysql-client                                      && \
    apt-get install -y postgresql-client-14                                      && \ 
    apt-get install -y redis-tools                                               && \
    apt-get install -y ncurses-dev libtolua-dev exuberant-ctags gdb              && \
    apt-get install -y ca-certificates curl wget bind9-utils                     && \
    apt-get install -y git g++ gcc libc6-dev make pkg-config vim                 && \
    apt-get install -y dnsutils                                                  && \
    apt-get install -y iputils-ping                                              && \
    apt-get install -y net-tools                                                 && \
    apt-get clean && rm -rf /var/lib/apt/lists/*                                 && \
    chmod a+x /usr/bin/ttyd-kubectl.sh && bash /usr/bin/ttyd-kubectl.sh          && \
    go install github.com/nsf/gocode@latest                                      && \
    go install golang.org/x/tools/cmd/goimports@latest                           && \
    go install github.com/rogpeppe/godef@latest                                  && \
    go install golang.org/x/tools/cmd/gorename@latest                            && \
    go install github.com/kisielk/errcheck@latest                                && \
    go install github.com/go-delve/delve/cmd/dlv@latest                          && \
    GO111MODULE=on go install golang.org/x/tools/gopls@latest                    && \
    mv /go/bin/* /usr/local/go/bin && rm -rf /go/src/* /go/pkg                   && \
    vim +PlugInstall +qall && chmod a+x /usr/bin/start-terminal.sh

ENV USER_TOKEN ""
ENV APISERVER "https://apiserver.cluster.local:6443"
ENV USER_NAME "admin"
ENV NAMESPACE "default"

EXPOSE 8080

CMD ["sh","/usr/bin/start-terminal.sh"]
