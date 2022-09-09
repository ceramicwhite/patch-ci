FROM ghcr.io/maxisoft/nim-docker-images/nim as build

ARG  REPO=https://github.com/zedeus/nitter.git

RUN apk update \
        &&  apk add libsass-dev \
        libffi-dev \
        openssl-dev \
        pcre \
        unzip \
        git \
        &&  mkdir -p /build

WORKDIR /build

RUN set -ex \
        &&  git clone $REPO . \
        &&  nimble install -y --depsOnly \
        &&  nimble build -y -d:release --passC:"-flto" --passL:"-flto" \
        &&  strip -s nitter \
        &&  nimble scss

COPY ./about.md /build/public/md/about.md

RUN nimble md
# ---------------------------------------------------------------------

FROM alpine:3.16.2

ENV REDIS_HOST="redis" \
        REDIS_PASS="moneyprintergobrrr" \
        REDIS_PORT=6379 \
        NITTER_HTTPS="false" \
        NITTER_HOST="nitter.net" \
        NITTER_NAME="nitter" \
        NITTER_THEME="auto_(twitter)" \
        REPLACE_TWITTER="nitter.net" \
        REPLACE_YOUTUBE="piped.kavin.rocks" \
        REPLACE_REDDIT="teddit.net" \
        REPLACE_INSTAGRAM=""

COPY ./entrypoint.sh /entrypoint.sh
COPY ./nitter.conf.pre /dist/nitter.conf.pre

COPY --from=build /build/nitter /usr/local/bin
COPY --from=build /build/public /build/public

RUN set -eux \
        &&  apk add --no-cache curl pcre su-exec

USER 1000:1000

WORKDIR /data
VOLUME  /data

EXPOSE  8080

ENTRYPOINT ["/entrypoint.sh"]