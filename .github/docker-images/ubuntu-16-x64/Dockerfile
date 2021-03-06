FROM ubuntu:16.04

SHELL ["/bin/bash", "-c"]

###############################################################################
# Update apt
###############################################################################
RUN apt-get update -qq

###############################################################################
# Install prereqs
###############################################################################
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install \
    git \
    curl \
    sudo \
    # Python
    python3 \
    python3-dev \
    python3-pip \
    build-essential \
    cmake \
    # For PPAs
    software-properties-common \
    apt-transport-https

###############################################################################
# Python/AWS CLI
###############################################################################
RUN python3 -m pip install --upgrade pip setuptools virtualenv \
    && python3 -m pip install --upgrade awscli \
    && aws --version

###############################################################################
# OpenSSL
###############################################################################
WORKDIR /tmp/build
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install gcc g++ \
    && ([ -d /opt/openssl ] && rm -rf /opt/openssl) || true \
    && git clone https://github.com/openssl/openssl.git \
    && pushd openssl \
    && git checkout OpenSSL_1_1_1-stable \
    && ./config -fPIC \
        no-md2 no-rc5 no-rfc3779 no-sctp no-ssl-trace no-zlib no-hw no-mdc2 \
        no-seed no-idea no-camellia no-bf no-dsa no-ssl3 no-capieng \
        no-unit-test no-tests \
        -DSSL_FORBID_ENULL -DOPENSSL_NO_DTLS1 -DOPENSSL_NO_HEARTBEATS \
        --prefix=/opt/openssl --openssldir=/opt/openssl \
    && make -j \
    && make install_sw \
    && LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/openssl/lib /opt/openssl/bin/openssl version \
    && DEBIAN_FRONTEND=noninteractive apt-get remove -y gcc g++ \
    && apt autoremove -y
    
###############################################################################
# Cleanup
###############################################################################
RUN apt-get clean \
    && rm -rf /tmp/*

###############################################################################
# Install builder package
###############################################################################
ADD builder /usr/local/bin/builder
COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
