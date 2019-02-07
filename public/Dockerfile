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

# create site dir:
RUN mkdir -pm0111 $MYTIME_SITE_DIR

WORKDIR $MYTIME_SITE_DIR

# copy only public files:
RUN mkdir -pm0111 ${MYTIME_SITE_DIR}/public
COPY --from=0 /usr/src/mytime/public ${MYTIME_SITE_DIR}/public

# install some utils and fix permissions:
RUN apk add --no-cache tzdata findutils \
	&& find . -type f -exec chmod 0444 {} \; \
	&& find . -type d -exec chmod 0111 {} \; \
	&& apk del findutils

# publish mytime public dir for static serve:
VOLUME ${MYTIME_SITE_DIR}/public

# run dummy command for public: 
CMD [ "tail", "-F", "/dev/null" ]

