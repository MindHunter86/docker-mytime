# -*- coding: utf-8 -*-
# vim: ft=Dockerfile

# mytime deploy Dockerfile - public
ARG MYTIME_BASE_IMAGE="registry.is-docker.mh00p.net/mytime-base:latest"
ARG MYTIME_SITE_DIR="/srv/mytime"


# fucking hacks for docker COPY --from =(
# https://github.com/moby/moby/issues/34482
FROM $MYTIME_BASE_IMAGE as source


# so an another hack for excludes in COPY instruction
# this hack helps to reduce image size by removing layer with "rm -rf command"
# fucking hacks for fucking docker #2
FROM busybox:latest as cleaner
LABEL maintainer="vkom <admin@vkom.cc>"

ARG MYTIME_SITE_DIR
WORKDIR $MYTIME_SITE_DIR

COPY --chown=root:root --from=source /usr/src/mytime/ $MYTIME_SITE_DIR

# remove backend, batch and bot files:
RUN rm -vrf cache views node_modules app.js bot.js

# fix permissions for anonymous users
# and remove listing permissions for public:
RUN chmod -R a=rX $MYTIME_SITE_DIR \
  && chmod 0111 $MYTIME_SITE_DIR/public


# working production image
FROM alpine:latest
LABEL maintainer="vkom <admin@vkom.cc>"

# env for cmd exec:
ARG MYTIME_SITE_DIR
WORKDIR $MYTIME_SITE_DIR

# create log dir: XXX - log dir used only with forever
# RUN mkdir -pm0170 /var/log/mytime \
#	&& chgrp w3mytime /var/log/mytime

STOPSIGNAL SIGKILL

# run dummy cmd for public serve:
CMD /bin/rm -rfv /media/mytime ; /bin/cp -av /srv/mytime/. /media/mytime ; cat /dev/stdout
