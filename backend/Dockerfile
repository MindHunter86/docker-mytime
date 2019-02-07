# -*- coding: utf-8 -*-
# vim: ft=Dockerfile

# mytime-backend deploy Dockerfile

ARG MYTIME_RELEASE_URL=https://example.com
ARG MYTIME_SITE_DIR=/srv/mytime

## BUILDER:
FROM node:10-alpine
LABEL maintainer="vkom <admin@mh00p.net"

ARG MYTIME_RELEASE_URL
ARG MYTIME_SITE_DIR

# prepare build directory:
RUN mkdir -pm0700 /usr/src/mytime

WORKDIR /usr/src/mytime

# download and unpack mytime release:
RUN curl -LSs ${MYTIME_RELEASE_URL} | tar xvC .

# npm install:
RUN npm i

# remove package metadata files for production:
RUN rm -vf package.json \
	&& rm -vf package-lock.json

# prepare additional directories:
RUN mkdir -pm0700 cache


## RELEASE:
FROM node:10-slim
LABEL maintainer="vkom <admin@mh00p.net"

ARG MYTIME_SITE_DIR

# create mytime user/group:
RUN addgroup -S w3mytime \
	&& adduser -D -S -h $MYTIME_SITE_DIR -s /sbin/nologin -G w3mytime w3mytime

# create site dir:
RUN mkdir -pm0110 $MYTIME_SITE_DIR \
	&& chgrp w3mytime $MYTIME_SITE_DIR

WORKDIR $MYTIME_SITE_DIR

# copy files:
COPY --from=0 /usr/src/mytime/ ${MYTIME_SITE_DIR}

# remove public and bot files:
RUN rm -rf public \
	&& rm -f bot.js

# install some utils and fix permissions:
RUN apk add --no-cache tzdata findutils \
	&& find . -type f -exec chmod 0440 {} \; \
	&& find . -type d -exec chmod 0110 {} \; \
	&& chmod 0170 cache \
	&& apk del findutils

# create log dir:
RUN mkdir -pm0170 /var/log/mytime \
	&& chgrp w3mytime /var/log/mytime

# expose backend port:
EXPOSE 8080

# set user for next CMD instruction:
USER w3mytime:w3mytime

# run backend: 
CMD [ "node", "${MYTIME_SITE_DIR}/app.js"] 

