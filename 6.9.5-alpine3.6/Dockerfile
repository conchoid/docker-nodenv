FROM node:6.9.5-alpine

RUN set -x \
  && GLIBC_PKG_VERSION="${GLIBC_PKG_VERSION:-2.26-r0}" \
  && GLIBC_URL="https://github.com/sgerrand/alpine-pkg-glibc/releases/download/$GLIBC_PKG_VERSION" \
  && GLIBC_SSH_KEY="/etc/apk/keys/sgerrand.rsa.pub" \
  && GLIBC_PKG_FILE="glibc-$GLIBC_PKG_VERSION.apk" \
  && GLIBC_BIN_PKG_FILE="glibc-bin-$GLIBC_PKG_VERSION.apk" \
  && apk add --no-cache --virtual=.fetchdeps wget ca-certificates \
  && wget -q -O "$GLIBC_SSH_KEY" "https://raw.githubusercontent.com/sgerrand/alpine-pkg-glibc/master/sgerrand.rsa.pub" \
  && wget -q "$GLIBC_URL/$GLIBC_PKG_FILE" "$GLIBC_URL/$GLIBC_BIN_PKG_FILE" \
  && apk add --no-cache "$GLIBC_PKG_FILE" "$GLIBC_BIN_PKG_FILE" \
  && apk del .fetchdeps \
  && rm "$GLIBC_SSH_KEY" \
  && rm ~/.wget-hsts \
  && rm "$GLIBC_PKG_FILE" "$GLIBC_BIN_PKG_FILE"

RUN apk add --no-cache \
  bash="4.3.42-r5" \
  build-base="0.4-r1" \
  binutils-gold="2.26-r1" \
  git="2.8.6-r0" \
  linux-headers="4.4.6-r1" \
  openssl="1.0.2n-r0" \
  python="2.7.12-r0" \
  wget="1.18-r2"

ENV NODENV_ROOT /opt/nodenv
ENV PATH "$NODENV_ROOT/shims:$NODENV_ROOT/bin:$PATH"

RUN set -x \
  && nodenv_version="v1.1.2" \
  && git clone https://github.com/nodenv/nodenv.git ${NODENV_ROOT} \
  && cd ${NODENV_ROOT} && git checkout ${nodenv_version} \
  && src/configure \
  && make -C src \
  && node_build_version="v2.6.19" \
  && git clone https://github.com/nodenv/node-build.git ${NODENV_ROOT}/plugins/node-build \
  && cd ${NODENV_ROOT}/plugins/node-build && git checkout ${node_build_version} \
  && npm_migrate_version="0.1.0" \
  && git clone https://github.com/nodenv/nodenv-npm-migrate.git ${NODENV_ROOT}/plugins/nodenv-npm-migrate \
  && cd ${NODENV_ROOT}/plugins/nodenv-npm-migrate && git checkout ${npm_migrate_version} \
  && rm -rf ${NODENV_ROOT}/.git ${NODENV_ROOT}/plugins/node-build/.git ${NODENV_ROOT}/plugins/nodenv-npm-migrate/.git
