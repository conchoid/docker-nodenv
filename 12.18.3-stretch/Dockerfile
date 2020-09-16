FROM node:12.18.3-stretch-slim

# Preset locale to en_US.UTF-8
# https://docs.docker.com/samples/library/debian/#locales
RUN apt-get update && apt-get install -y locales \
    && apt-get clean && rm -rf /var/lib/apt/lists/* \
    && localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

RUN apt-get update && apt-get install -y  \
        git \
        curl \
        pkg-config \
        libx11-dev \
        libxkbfile-dev \
        libsecret-1-dev \
        node-gyp \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

ENV NODENV_ROOT /opt/nodenv
ENV PATH "$NODENV_ROOT/shims:$NODENV_ROOT/bin:$PATH"

# Install nodenv
RUN nodenv_version="v1.4.0" \
  && git clone https://github.com/nodenv/nodenv.git ${NODENV_ROOT} \
  && cd ${NODENV_ROOT} && git checkout ${nodenv_version} \
  && src/configure && make -C src \
  && node_build_version="v4.9.7" \
  && git clone https://github.com/nodenv/node-build.git ${NODENV_ROOT}/plugins/node-build \
  && cd ${NODENV_ROOT}/plugins/node-build && git checkout ${node_build_version} \
  && node_build_jxcore_version="1b6cdf343b767e77b9f1522d5c9fa111c5373794" \
  && git clone https://github.com/nodenv/node-build-jxcore.git $(nodenv root)/plugins/node-build-jxcore \
  && cd $(nodenv root)/plugins/node-build-jxcore && git checkout ${node_build_jxcore_version} \
  && npm_migrate_version="v0.1.1" \
  && git clone https://github.com/nodenv/nodenv-npm-migrate.git ${NODENV_ROOT}/plugins/nodenv-npm-migrate \
  && cd ${NODENV_ROOT}/plugins/nodenv-npm-migrate && git checkout ${npm_migrate_version} \
  && find ${NODENV_ROOT} -type d -name ".git" -exec rm -r "{}" \+ \
  && curl -fsSL https://github.com/nodenv/nodenv-installer/raw/master/bin/nodenv-doctor | bash \
  && nodenv install --list
