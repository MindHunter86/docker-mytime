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
RUN echo curl -LSs ${MYTIME_RELEASE_URL} tar zxvC .
RUN apk add --no-cache curl \
	&& curl -LSs ${MYTIME_RELEASE_URL} | tar zxvC .

# install python for as npm plugin dependence:
RUN apk add --no-cache build-base python

# npm install:
RUN npm i

# remove package metadata files for production:
RUN rm -vf package.json \
	&& rm -vf package-lock.json

# fix permissions:
RUN apk add --no-cache findutils \
	&& find . -type f -exec chmod 0440 {} \; \
	&& find . -type d -exec chmod 2110 {} \; \
	&& apk del findutils

# prepare additional directories:
RUN mkdir -pm0170 cache


## RELEASE:
FROM node:10-slim
LABEL maintainer="vkom <admin@mh00p.net"

ARG MYTIME_SITE_DIR
ENV MYTIME_SITE_DIR $MYTIME_SITE_DIR

# create mytime user/group:
RUN addgroup --system w3mytime \
	&& adduser --system --disabled-password --home "$MYTIME_SITE_DIR" --shell /sbin/nologin --ingroup w3mytime w3mytime

# create site dir:
RUN mkdir -pm0110 $MYTIME_SITE_DIR \
	&& chgrp w3mytime $MYTIME_SITE_DIR

WORKDIR $MYTIME_SITE_DIR

# copy files:
COPY --chown=root:w3mytime --from=0 /usr/src/mytime/ $MYTIME_SITE_DIR

# remove public and bot files:
RUN rm -vrf public bot.js

# MYTIME_SITE_DIR files listing for debugging:
RUN ls -la $MYTIME_SITE_DIR

# create log dir: XXX - log dir used only with forever
# RUN mkdir -pm0170 /var/log/mytime \
#	&& chgrp w3mytime /var/log/mytime

# expose backend port:
EXPOSE 8080

# set user for next CMD instruction:
USER w3mytime:w3mytime

# run backend: 
CMD node ${MYTIME_SITE_DIR}/app.js

