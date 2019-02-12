# -*- coding: utf-8 -*-
# vim: ft=Dockerfile

# mytime deploy Dockerfile - csgoman

ARG MYTIME_SITE_DIR="/srv/mytime"


# mytime deploy Dockerfile - base image for csgoman
FROM node:10-alpine as base
LABEL maintainer="vkom <admin@mh00p.net"

# prepare build directory:
RUN mkdir -pm0700 /usr/src/mytime

WORKDIR /usr/src/mytime

# install python for as npm plugin dependence:
RUN apk add --no-cache build-base python

# unpack release tar archive and npm install:
ADD dist.tar .

RUN npm i

# remove source archive and package metadata files for production:
RUN rm -vf package.json package-lock.json

# fix permissions:
RUN apk add --no-cache findutils \
	&& find . -type f -exec chmod 0440 {} \; \
	&& find . -type d -exec chmod 2110 {} \; \
	&& apk del findutils

# debug:
RUN ls -la /usr/src/mytime


# mytime deploy Dockerfile - release image
FROM node:10-slim
LABEL maintainer="vkom <admin@mh00p.net"

# env for cmd exec:
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
COPY --chown=root:w3mytime --from=base /usr/src/mytime/ $MYTIME_SITE_DIR

# MYTIME_SITE_DIR files listing for debugging:
RUN ls -la $MYTIME_SITE_DIR

# create log dir: XXX - log dir used only with forever
# RUN mkdir -pm0170 /var/log/mytime \
#	&& chgrp w3mytime /var/log/mytime

# set user for next CMD instruction:
USER w3mytime:w3mytime

# run backend: 
CMD node ${MYTIME_SITE_DIR}/app.js
