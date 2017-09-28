FROM gcr.io/google-containers/debian-base-amd64:0.1
MAINTAINER Adrian Lopez <adrianlzt@gmail.com>
LABEL Description="Fluent Bit docker image" Vendor="Fluent Organization" Version="1.1"

# Fluent Bit version
ENV FLB_VERSION master

ENV FLB_TARBALL http://github.com/fluent/fluent-bit/archive/$FLB_VERSION.zip

ENV GOPATH /gopath

ENV GO_INSTALL https://storage.googleapis.com/golang/go1.9.linux-amd64.tar.gz

ENV PATH /usr/local/go/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

RUN mkdir -p /fluent-bit/bin /fluent-bit/etc $GOPATH

# Entry point
CMD ["/fluent-bit/bin/fluent-bit", "-e", "/fluent-bit/out_redis.so", "-c", "/fluent-bit/etc/fluent-bit.conf"]

RUN apt-get -qq update \
    && apt-get install -y -qq \
       ca-certificates \
       build-essential \
       cmake \
       make \
       sudo \
       wget \
       unzip \
       libsystemd-dev \
       git \
    && apt-get install -y -qq --reinstall lsb-base lsb-release \
    && wget -O "/tmp/fluent-bit-${FLB_VERSION}.zip" ${FLB_TARBALL} \
    && cd /tmp && unzip "fluent-bit-$FLB_VERSION.zip" \
    && cd "fluent-bit-$FLB_VERSION"/build/ \
    && cmake -DFLB_DEBUG=On -DFLB_TRACE=On -DFLB_JEMALLOC=On -DFLB_BUFFERING=On ../ \
    && make \
    && install bin/fluent-bit /fluent-bit/bin/ \
    && cd /usr/local/ && wget -O "go.tgz" ${GO_INSTALL} && tar xf go.tgz \
    && go get github.com/adrianlzt/fluent-bit-redis \
    && cd /gopath/src/github.com/adrianlzt/fluent-bit-redis && make \
    && cp out_redis.so /fluent-bit/ \
    && apt-get remove --purge --auto-remove -y -qq \
       build-essential \
       cmake \
       make \
       wget \
       unzip \
       libsystemd-dev \
       git \
    && rm -rf /tmp/* $GOPATH

# Configuration files
COPY fluent-bit.conf /fluent-bit/etc/
