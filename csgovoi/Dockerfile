# Dockerfile : MyTime csgovoi

FROM node:16-alpine as builder
LABEL maintainer="mindhunter86 <admin@mh00p.net>"

ARG MYTIME_RELEASE_URL=https://example.com

WORKDIR /usr/src/mytime-csgovoi

# download and unpack mytime release:
RUN apk add --no-cache curl \
	&& curl -fq -SsL ${MYTIME_RELEASE_URL} | tar zxvC . \
  && apk del curl

# project build instructions:
RUN npm install -g yarn && yarn

RUN rm -rvf package*.json *md .git* bin

# remove package metadata files for production:
RUN rm -vf package.json \
	&& rm -vf package-lock.json

# fix permissions:
RUN apk add --no-cache findutils \
	&& find . -type f -exec chmod 0440 {} \; \
	&& find . -type d -exec chmod 2150 {} \; \
	&& apk del findutils

# debug:
RUN ls -la /usr/src/mytime


FROM node:16-alpine
LABEL maintainer="mindhunter86 <admin@mh00p.net>"

# env for cmd exec:
ARG MYTIME_SITE_DIR
ENV MYTIME_SITE_DIR $MYTIME_SITE_DIR

WORKDIR $MYTIME_SITE_DIR

# create mytime user/group:
RUN set -e \
  && addgroup --system w3mytime \
	&& adduser --system --disabled-password --home "$MYTIME_SITE_DIR" --shell /sbin/nologin --ingroup w3mytime w3mytime \
  && chgrp w3mytime $MYTIME_SITE_DIR \
  && chmod 2110 $MYTIME_SITE_DIR

# copy files from builder container
COPY --chown=root:w3mytime --from=builder /usr/src/mytime-csgovoi/index.js,/usr/src/mytime-csgovoi/node_modules,/usr/src/mytime-csgovoi/src ${MYTIME_SITE_DIR}/

# debug
RUN ls -la ${MYTIME_SITE_DIR}/

EXPOSE 6464
USER w3mytime:w3mytime

CMD ["node", "index"]
