# -*- coding: utf-8 -*-
# vim: ft=Dockerfile

# mytime deploy Dockerfile - public
ARG MYTIME_BASE_IMAGE="registry.is-docker.mh00p.net/mytime-base:latest"
ARG MYTIME_SITE_DIR="/srv/mytime"


# fucking hacks for docker COPY --from =(
# https://github.com/moby/moby/issues/34482
FROM $MYTIME_BASE_IMAGE as source


FROM alpine:latest
LABEL maintainer="vkom <admin@mh00p.net"

# env for cmd exec:
ARG MYTIME_SITE_DIR

# create site dir:
RUN mkdir -pm0111 $MYTIME_SITE_DIR

WORKDIR $MYTIME_SITE_DIR

# copy files:
COPY --chown=root:root --from=source /usr/src/mytime/ $MYTIME_SITE_DIR

# fix permissions for anonymous users: XXX FIXME - remove --X for folders in workdir!!
RUN chmod o+rx $MYTIME_SITE_DIR

# remove public and bot files:
RUN rm -vrf cache views node_modules app.js bot.js

# MYTIME_SITE_DIR files listing for debugging:
RUN ls -la $MYTIME_SITE_DIR

# create log dir: XXX - log dir used only with forever
# RUN mkdir -pm0170 /var/log/mytime \
#	&& chgrp w3mytime /var/log/mytime

STOPSIGNAL SIGKILL

# run dummy cmd for public serve:
CMD ["sh", "-c", "'rm -rfv /media/mytime; cp -av /srv/mytime /media/mytime; cat -'"]
