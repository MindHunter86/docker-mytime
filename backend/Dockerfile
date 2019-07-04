# -*- coding: utf-8 -*-
# vim: ft=Dockerfile

# mytime deploy Dockerfile - backend
ARG MYTIME_BASE_IMAGE="registry.is-docker.mh00p.net/mytime-base:latest"
ARG MYTIME_SITE_DIR="/srv/mytime"


# fucking hacks for docker COPY --from =(
# https://github.com/moby/moby/issues/34482
FROM $MYTIME_BASE_IMAGE as source


FROM node:10-alpine
LABEL maintainer="vkom <admin@mh00p.net"

# env for cmd exec:
ARG MYTIME_SITE_DIR
ENV MYTIME_SITE_DIR $MYTIME_SITE_DIR

# create mytime user/group:
RUN addgroup --system w3mytime \
	&& adduser --system --disabled-password --home "$MYTIME_SITE_DIR" --shell /sbin/nologin --ingroup w3mytime w3mytime

# create site dir:
RUN mkdir -pm2110 $MYTIME_SITE_DIR \
	&& chgrp w3mytime $MYTIME_SITE_DIR

WORKDIR $MYTIME_SITE_DIR

# copy files:
COPY --chown=root:w3mytime --from=source /usr/src/mytime/ $MYTIME_SITE_DIR

# XXX - 2DELETE
RUN touch is_master.flag

# fix permissions after COPY for site dir:
RUN chmod 2110 $MYTIME_SITE_DIR

# remove public and bot files:
RUN rm -vrf public bot.js

# MYTIME_SITE_DIR files listing for debugging:
RUN ls -la $MYTIME_SITE_DIR

# install csgocups requirements:
RUN apk add --no-cache musl

# install tzdata and set Moscow timezone:
RUN apk add --no-cache tzdata \
	&& ln -s /usr/share/zoneinfo/Europe/Moscow /etc/localtime \
	&& echo 'Europe/Moscow' > /etc/timezone

# create log dir: XXX - log dir used only with forever
# RUN mkdir -pm0170 /var/log/mytime \
#	&& chgrp w3mytime /var/log/mytime

# expose backend port:
EXPOSE 8080

# set user for next CMD instruction:
USER w3mytime:w3mytime

# run backend: 
CMD node ${MYTIME_SITE_DIR}/app.js
