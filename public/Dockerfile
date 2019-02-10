# -*- coding: utf-8 -*-
# vim: ft=Dockerfile

# mytime-public deploy Dockerfile

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
RUN apk add --no-cache curl \
	&& curl -LSs ${MYTIME_RELEASE_URL} | tar zxvC .

# remove all backend files:
RUN rm -vfr views *.js *.json

# fix permissions:
RUN apk add --no-cache findutils \
	&& find . -type f -exec chmod 0444 {} \; \
	&& find . -type d -exec chmod 0111 {} \; \
	&& apk del findutils


## RELEASE:
FROM node:10-slim
LABEL maintainer="vkom <admin@mh00p.net"

ARG MYTIME_SITE_DIR
ENV MYTIME_SITE_DIR $MYTIME_SITE_DIR

WORKDIR $MYTIME_SITE_DIR

# copy files:
COPY --chown=root:root --from=0 /usr/src/mytime/ $MYTIME_SITE_DIR

# MYTIME_SITE_DIR files listing for debugging:
RUN ls -la $MYTIME_SITE_DIR

STOPSIGNAL SIGKILL

# run backend: 
CMD ["cat", "/dev/stdout"]

