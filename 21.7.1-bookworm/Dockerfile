FROM node:16.20.1-bookworm-slim AS node16
FROM node:18.20.0-bookworm-slim AS node18
FROM node:20.12.0-bookworm-slim AS node20
FROM node:21.7.1-bookworm-slim AS node21

FROM node:21.7.1-bookworm-slim

# install build deps and set locale (Preset locale to en_US.UTF-8)
RUN apt-get update && apt-get install -y \
      curl \
      git \
      git-lfs \
      libsecret-1-dev \
      libx11-dev \
      libxkbfile-dev \
      locales \
      node-gyp \
      pkg-config \
    && apt-get clean && rm -rf /var/lib/apt/lists/* \
    && localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8

ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

ENV NODENV_ROOT /opt/nodenv
ENV PATH "$NODENV_ROOT/shims:$NODENV_ROOT/bin:$PATH"

# Install nodenv
RUN nodenv_version="v1.4.1" \
  && git clone https://github.com/nodenv/nodenv.git ${NODENV_ROOT} \
  && cd ${NODENV_ROOT} && git checkout ${nodenv_version} \
  && src/configure && make -C src \
  && node_build_version="v4.9.140" \
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


# Install all latest major Nodejs versions excludes older than one year since EOL.
# https://github.com/nodejs/Release#end-of-life-releases

ENV ROCRO_NODE16 "16.20.1"
ENV ROCRO_NODE18 "18.20.0"
ENV ROCRO_NODE20 "20.12.0"
ENV ROCRO_NODE21 "21.7.1"
ENV ROCRO_PREINSTALLED_VERSIONS  "\
${ROCRO_NODE16}\n\
${ROCRO_NODE18}\n\
${ROCRO_NODE20}\n\
${ROCRO_NODE21}"

COPY --from=node16 "/usr/local/bin/node" "${NODENV_ROOT}/versions/${ROCRO_NODE16}/bin/"
COPY --from=node18 "/usr/local/bin/node" "${NODENV_ROOT}/versions/${ROCRO_NODE18}/bin/"
COPY --from=node20 "/usr/local/bin/node" "${NODENV_ROOT}/versions/${ROCRO_NODE20}/bin/"
COPY --from=node21 "/usr/local/bin/node" "${NODENV_ROOT}/versions/${ROCRO_NODE21}/bin/"

COPY --from=node16 "/usr/local/lib/node_modules" "${NODENV_ROOT}/versions/${ROCRO_NODE16}/lib/node_modules"
COPY --from=node18 "/usr/local/lib/node_modules" "${NODENV_ROOT}/versions/${ROCRO_NODE18}/lib/node_modules"
COPY --from=node20 "/usr/local/lib/node_modules" "${NODENV_ROOT}/versions/${ROCRO_NODE20}/lib/node_modules"
COPY --from=node21 "/usr/local/lib/node_modules" "${NODENV_ROOT}/versions/${ROCRO_NODE21}/lib/node_modules"

RUN mkdir -p "${NODENV_ROOT}/versions/${ROCRO_NODE20}/bin/" "${NODENV_ROOT}/versions/${ROCRO_NODE20}/lib/" \
    && cp "$(nodenv which node)" "${NODENV_ROOT}/versions/${ROCRO_NODE20}/bin/" \
    && cp -R "/usr/local/lib/node_modules" "${NODENV_ROOT}/versions/${ROCRO_NODE20}/lib/" \
    && npm install -g pnpm@8.15.8 \
    && echo ${ROCRO_PREINSTALLED_VERSIONS} | while read version;do \
      NPM_SRC="${NODENV_ROOT}/versions/${version}/lib/node_modules/npm/bin/npm-cli.js" \
      && NPM_DST="${NODENV_ROOT}/versions/${version}/bin/npm" \
      # Override the shebang line of npm-cli.js to use the "node" path dynamically.
      && sed -i -E "1s/#.+/#!\/usr\/bin\/env node/" "${NPM_SRC}" \
      && ln -s -f "${NPM_SRC}" "${NPM_DST}";done \
    && nodenv rehash \
    && npm set progress false --global --quiet \
    && [ "$(nodenv versions --bare | xargs)" = "$ROCRO_NODE16 $ROCRO_NODE18 $ROCRO_NODE20 $ROCRO_NODE21" ]
