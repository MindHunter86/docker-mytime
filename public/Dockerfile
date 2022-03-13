# -*- coding: utf-8 -*-
# vim: ft=Dockerfile

# mytime deploy Dockerfile - public
ARG MYTIME_BASE_IMAGE="registry.is-docker.mh00p.net/mytime-base:latest"
ARG MYTIME_SITE_DIR="/srv/mytime"
ARG THTTPD_VERSION=2.29


# fucking hacks for docker COPY --from =(
# https://github.com/moby/moby/issues/34482
FROM $MYTIME_BASE_IMAGE as source


# so an another hack for excludes in COPY instruction
# this hack helps to reduce image size by removing layer with "rm -rf command"
# fucking hacks for fucking docker #2
# TODO : maybe this shit is needed in optimizations with .dockerignore file =)
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


# brotli and 
# use webp + brotli
FROM alpine:latest as optimizer
LABEL maintainer="vkom <admin@vkom.cc>"

ARG MYTIME_SITE_DIR
WORKDIR $MYTIME_SITE_DIR

# mytime
# TODO : maybe this shit is needed in optimizations with .dockerignore file =)
COPY --chown=root:root --from=cleaner $MYTIME_SITE_DIR $MYTIME_SITE_DIR

RUN apk add --no-cache brotli libwebp-tools findutils

RUN ls -la

# brotli compressing
# TODO remove source files
RUN find public/js/ public/css/ -type f -exec brotli -kf {} \;

# make webp image copy (png + jpeg only)
# TODO not only jpg, add jpEg
RUN find -type f -and \( -iname "*.png" -o -iname "*.jpg" \) -exec sh -c "echo {} | sed 's/\.[^.]*$/.webp/g' | cwebp {} -o \$(cat -)" \;

RUN ls -la public
RUN ls -la public/i
RUN ls -la public/css
RUN ls -la public/js


# 2DELETE BLOCK
# =================================
# OLD STATIC FILE SERVING VERSION :

# working production image
FROM alpine:latest
LABEL maintainer="vkom <admin@vkom.cc>"

# env for cmd exec:
ARG MYTIME_SITE_DIR
WORKDIR $MYTIME_SITE_DIR

COPY --chown=root:root --from=optimizer $MYTIME_SITE_DIR $MYTIME_SITE_DIR

STOPSIGNAL SIGKILL

# run dummy cmd for public serve:
CMD /bin/rm -rfv /media/mytime ; /bin/cp -av /srv/mytime/. /media/mytime ; cat /dev/stdout
