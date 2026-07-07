# Docker Hub: conchoid/docker-nodenv:v1.6.2-2-24.18.0-trixie
FROM node:22.23.1-trixie-slim AS node22
FROM node:24.18.0-trixie-slim AS node24
FROM node:26.4.0-trixie-slim AS node26

FROM node:24.18.0-trixie-slim

# install build deps and set locale (Preset locale to en_US.UTF-8)
RUN apt-get update && apt-get upgrade -y && apt-get install -y --no-install-recommends \
      ca-certificates \
      curl \
      git \
      libatomic1 \
      libsecret-1-dev \
      libx11-dev \
      libxkbfile-dev \
      locales \
      node-gyp \
      pkg-config \
    && apt-get clean && rm -rf /var/lib/apt/lists/* \
    && localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8

COPY --from=conchoid/debian:trixie-slim /usr/local/bin/git-lfs /usr/local/bin/git-lfs
RUN git lfs install

ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8

ENV NODENV_ROOT=/opt/nodenv
ENV PATH="$NODENV_ROOT/shims:$NODENV_ROOT/bin:$PATH"

# Install nodenv
RUN nodenv_version="v1.6.2" \
  && git clone https://github.com/nodenv/nodenv.git ${NODENV_ROOT} \
  && cd ${NODENV_ROOT} && git checkout ${nodenv_version} \
  && node_build_version="v5.4.44" \
  && git clone https://github.com/nodenv/node-build.git ${NODENV_ROOT}/plugins/node-build \
  && cd ${NODENV_ROOT}/plugins/node-build && git checkout ${node_build_version} \
  && git clone https://github.com/nodenv/node-build-jxcore.git $(nodenv root)/plugins/node-build-jxcore \
  && cd $(nodenv root)/plugins/node-build-jxcore && git checkout 1b6cdf343b767e77b9f1522d5c9fa111c5373794 \
  && npm_migrate_version="v0.1.1" \
  && git clone https://github.com/nodenv/nodenv-npm-migrate.git ${NODENV_ROOT}/plugins/nodenv-npm-migrate \
  && cd ${NODENV_ROOT}/plugins/nodenv-npm-migrate && git checkout ${npm_migrate_version} \
  && find ${NODENV_ROOT} -type d -name ".git" -exec rm -r "{}" \+ \
  && curl -fsSL https://github.com/nodenv/nodenv-installer/raw/master/bin/nodenv-doctor | bash \
  && nodenv install --list


# Install all latest major Nodejs versions excludes older than one year since EOL.
# https://github.com/nodejs/Release#end-of-life-releases


ENV ROCRO_NODE22=22.23.1
ENV ROCRO_NODE24=24.18.0
ENV ROCRO_NODE26=26.4.0

ENV ROCRO_PREINSTALLED_VERSIONS="\
${ROCRO_NODE22}\n\
${ROCRO_NODE24}\n\
${ROCRO_NODE26}"

COPY --from=node22 "/usr/local/bin/node" "${NODENV_ROOT}/versions/${ROCRO_NODE22}/bin/"
COPY --from=node24 "/usr/local/bin/node" "${NODENV_ROOT}/versions/${ROCRO_NODE24}/bin/"
COPY --from=node26 "/usr/local/bin/node" "${NODENV_ROOT}/versions/${ROCRO_NODE26}/bin/"

COPY --from=node22 "/usr/local/lib/node_modules" "${NODENV_ROOT}/versions/${ROCRO_NODE22}/lib/node_modules"
COPY --from=node24 "/usr/local/lib/node_modules" "${NODENV_ROOT}/versions/${ROCRO_NODE24}/lib/node_modules"
COPY --from=node26 "/usr/local/lib/node_modules" "${NODENV_ROOT}/versions/${ROCRO_NODE26}/lib/node_modules"

RUN echo ${ROCRO_PREINSTALLED_VERSIONS} | while read version;do \
      npm install -g pnpm@11.6.0 \
      && NPM_SRC="${NODENV_ROOT}/versions/${version}/lib/node_modules/npm/bin/npm-cli.js" \
      && NPM_DST="${NODENV_ROOT}/versions/${version}/bin/npm" \
      # Update npm to latest if compatible (npm 11+ requires Node 20.17+ or 22.9+), otherwise update to latest supported major.
      && if "${NODENV_ROOT}/versions/${version}/bin/node" -e "const v=process.version.slice(1).split('.').map(Number); process.exit((v[0]>20 || (v[0]===20 && (v[1]>17 || (v[1]===17 && v[2]>=0)))) || (v[0]===22 && v[1]>=9) ? 0 : 1)"; then \
           "${NODENV_ROOT}/versions/${version}/bin/node" "${NPM_SRC}" install -g npm@latest; \
         else \
           "${NODENV_ROOT}/versions/${version}/bin/node" "${NPM_SRC}" install -g npm@10; \
         fi \
      && sed -i -E "1s/#.+/#!\/usr\/bin\/env node/" "${NPM_SRC}" \
      && ln -s -f "${NPM_SRC}" "${NPM_DST}";done \
    && nodenv rehash \
    && npm set progress false --global --quiet \
    && [ "$(nodenv versions --bare | xargs)" = "$ROCRO_NODE22 $ROCRO_NODE24 $ROCRO_NODE26" ]
