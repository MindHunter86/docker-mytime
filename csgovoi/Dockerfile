# Dockerfile : MyTime csgovoi

FROM node:16-alpine as builder
LABEL maintainer="mindhunter86 <admin@vkom.cc>"

ARG MYTIME_RELEASE_URL=https://example.com

ADD . /usr/src/mytime-csgovoi/
WORKDIR /usr/src/mytime-csgovoi/

# project build instructions:
RUN yarn

# fix permissions:
RUN apk add --no-cache findutils \
	&& find . -type f -exec chmod 0440 {} \; \
	&& find . -type d -exec chmod 2150 {} \; \
	&& apk del findutils

# debug:
RUN ls -la /usr/src/mytime-csgovoi/


FROM node:16-alpine
LABEL maintainer="mindhunter86 <admin@vkom.cc>"

# env for cmd exec:
ARG MYTIME_SITE_DIR="/srv/mytime"
ENV MYTIME_SITE_DIR $MYTIME_SITE_DIR

WORKDIR $MYTIME_SITE_DIR

# create mytime user/group:
RUN set -e \
  && addgroup --system w3mytime \
	&& adduser --system --disabled-password --home "$MYTIME_SITE_DIR" --shell /sbin/nologin --ingroup w3mytime w3mytime \
  && chgrp w3mytime $MYTIME_SITE_DIR \
  && chmod 2110 $MYTIME_SITE_DIR

# copy files from builder container
COPY --chown=root:w3mytime --from=builder /usr/src/mytime-csgovoi/index.js ${MYTIME_SITE_DIR}/
COPY --chown=root:w3mytime --from=builder /usr/src/mytime-csgovoi/package*.json ${MYTIME_SITE_DIR}/
COPY --chown=root:w3mytime --from=builder /usr/src/mytime-csgovoi/src ${MYTIME_SITE_DIR}/src/
COPY --chown=root:w3mytime --from=builder /usr/src/mytime-csgovoi/node_modules ${MYTIME_SITE_DIR}/node_modules/

# debug
RUN ls -la ${MYTIME_SITE_DIR}/

EXPOSE 6464
USER w3mytime:w3mytime

CMD ["node", "index"]
