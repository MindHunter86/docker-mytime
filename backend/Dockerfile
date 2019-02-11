# -*- coding: utf-8 -*-
# vim: ft=Dockerfile

# mytime deploy Dockerfile - backend
FROM node:10-slim
LABEL maintainer="vkom <admin@mh00p.net"

ARG MYTIME_GLOB_VERSION 20190101.0101
ARG MYTIME_SITE_DIR /srv/mytime

ENV MYTIME_SITE_DIR $MYTIME_SITE_DIR

# create mytime user/group:
RUN addgroup --system w3mytime \
	&& adduser --system --disabled-password --home "$MYTIME_SITE_DIR" --shell /sbin/nologin --ingroup w3mytime w3mytime

# create site dir:
RUN mkdir -pm0110 $MYTIME_SITE_DIR \
	&& chgrp w3mytime $MYTIME_SITE_DIR

WORKDIR $MYTIME_SITE_DIR

# copy files:
COPY --chown=root:w3mytime --from=mytime-base:${MYTIME_GLOB_VERSION} /usr/src/mytime/ $MYTIME_SITE_DIR

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
