FROM node:8.9.4-stretch

RUN apt-get update \
    && apt-get install -y locales git \
        apt-transport-https="1.4.8" \
        ca-certificates="20161130+nmu1+deb9u1" \
        gnupg="2.1.18-8~deb9u2" \
        binutils="2.28-5" \
        linux-headers-amd64="4.9+80+deb9u5" \
        openssl="1.1.0f-3+deb9u2" \
        libssl-dev="1.1.0f-3+deb9u2" \
        python-dev="2.7.13-2" \
        python="2.7.13-2" \
        wget="1.18-5+deb9u2" \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

ENV NODENV_ROOT /opt/nodenv
ENV PATH "$NODENV_ROOT/shims:$NODENV_ROOT/bin:$PATH"

RUN nodenv_version="v1.1.2" \
  && git clone https://github.com/nodenv/nodenv.git ${NODENV_ROOT} \
  && cd ${NODENV_ROOT} && git checkout ${nodenv_version} \
  && src/configure \
  && make -C src \
  && node_build_version="v3.0.0" \
  && git clone https://github.com/nodenv/node-build.git ${NODENV_ROOT}/plugins/node-build \
  && cd ${NODENV_ROOT}/plugins/node-build && git checkout ${node_build_version} \
  && npm_migrate_version="0.1.0" \
  && git clone https://github.com/nodenv/nodenv-npm-migrate.git ${NODENV_ROOT}/plugins/nodenv-npm-migrate \
  && cd ${NODENV_ROOT}/plugins/nodenv-npm-migrate && git checkout ${npm_migrate_version} \
  && rm -rf ${NODENV_ROOT}/.git ${NODENV_ROOT}/plugins/node-build/.git ${NODENV_ROOT}/plugins/nodenv-npm-migrate/.git
