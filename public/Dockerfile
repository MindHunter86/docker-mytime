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

# make webp image copy (png + jp*g only)
RUN find -type f -and \( -iname "*.png" -o -iname "*.jp*g" \) -exec sh -c "echo {} | sed 's/\.[^.]*$/.webp/g' | cwebp {} -o \$(cat -)" \;
RUN gif2webp public/i/preloader.gif -o public/i/preloader.webp

RUN ls -la public
RUN ls -la public/i
RUN ls -la public/css
RUN ls -la public/js

# and one more hack for reducing image size with static file serving feature
# there is building thttpd static binary for futher coping to "scratch" image.
# ! use this web server only for internal file serving !
# !!! last thttpd stable update dated 23 May 2018 !!!
FROM alpine:latest as thttpd
LABEL maintainer="vkom <admin@vkom.cc>"

ARG THTTPD_VERSION
ARG MYTIME_SITE_DIR
WORKDIR /usr/src

# Install all dependencies required for compiling thttpd
# Download thttpd sources
# Compile thttpd to a static binary which we can copy around
RUN apk add gcc musl-dev make
RUN wget -qO- http://www.acme.com/software/thttpd/thttpd-${THTTPD_VERSION}.tar.gz | tar zxvC . \
  && mv thttpd-${THTTPD_VERSION} thttpd
RUN cd thttpd \
  && echo -e 'webp\timage/webp\nwoff2\tfont/woff2\nico\timage/x-icon' >> mime_types.txt \
  && ./configure \
  && make CCOPT='-O2 -s -static' thttpd

# Create a non-root user to own the files and run our server
RUN adduser --system --disabled-password --home "$MYTIME_SITE_DIR" --shell /sbin/nologin w3mytime


# final image for mytime static serving
FROM scratch
LABEL maintainer="vkom <admin@vkom.cc>"

ARG MYTIME_SITE_DIR
WORKDIR $MYTIME_SITE_DIR

# thttpd
COPY --chown=root:root --from=thttpd /etc/passwd /etc/passwd
COPY --chown=root:root --from=thttpd /usr/src/thttpd/thttpd /

# mytime
# TODO : maybe this shit is needed in optimizations with .dockerignore file =)
COPY --chown=root:root --from=optimizer $MYTIME_SITE_DIR $MYTIME_SITE_DIR

USER w3mytime
EXPOSE 3000

# TODO add env variable for non-hardcode style
CMD ["/thttpd", "-D", "-h", "0.0.0.0", "-p", "3000", "-d", "public", "-u", "w3mytime", "-l", "-", "-M", "60"]


# THTTPD MANUAL:
# This will launch the server in foreground (-D), listening on host 0.0.0.0 (-h) and port 3000 (-p),
# serving all files inside /static-website (-d) that are accessible to static-user (-u).
# It will print access logs to STDOUT (-l -) and set the Cache-Control header to 60 seconds (-M).
# There are a couple of other neat features, like basic auth, throttling and virtual hosts,
# which you can read about in the documentation.
# https://linux.die.net/man/8/thttpd

# the idea for docker container size reducing
# originally from https://lipanski.com/posts/smallest-docker-image-static-website
# by Florin Lipan
