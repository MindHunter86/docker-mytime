# -*- coding: utf-8 -*-
# vim: ft=Dockerfile

# csgo docker container for mytime project
# https://mytime.gl

# srcds build on alpine manual:
#		https://wiki.alpinelinux.org/wiki/Running_glibc_programs#Source_dedicated_server

# 32bit (i686) pkgs for chroot downloaded from archlinux site
#		https://www.archlinux32.org/packages/

FROM 10-alpine
LABEL maintainer="vkom <admin@mh00p.net>"

# busybox,glibc,tar download for future chroot:
# http://pool.mirror.archlinux32.org/i686/community/busybox-1.30.1-1.0-i686.pkg.tar.xz
# http://pool.mirror.archlinux32.org/i686/core/glibc-2.28-5.5-i686.pkg.tar.xz
# http://pool.mirror.archlinux32.org/i686/core/tar-1.32-1.0-i686.pkg.tar.xz
WORKDIR /usr/src/csgods
RUN mkdir -pm0700 arch32src && cd ./arch32src \
	&& wget -qO busybox-1.30.1-1.0-i686.pkg.tar.xz http://pool.mirror.archlinux32.org/i686/community/busybox-1.30.1-1.0-i686.pkg.tar.xz \
	&& wget -qO glibc-2.28-5.5-i686.pkg.tar.xz http://pool.mirror.archlinux32.org/i686/core/glibc-2.28-5.5-i686.pkg.tar.xz \
	&& wget -qO tar-1.32-1.0-i686.pkg.tar.xz http://pool.mirror.archlinux32.org/i686/core/tar-1.32-1.0-i686.pkg.tar.xz

RUN mkdir ./chroot/bin \
	&& mkdir ./chroot/dev \
	&& mkdir ./chroot/etc \
	&& mkdir ./chroot/proc \
	&& mkdir ./chroot/root \
	&& mkdir ./chroot/srv \
	&& ls -1 ./arch32src/*pkg.tar.xz | xargs -n1 | xargs -P3 -ri tar zxvC ./chroot -f {} \
	&& cp -v /etc/resolv.conf ./chroot/etc \
	&& ln -vs /bin/busybox ./chroot/bin/sh \
	&& ln -vs /bin/busybox ./chroot/bin/ln

RUN chroot ./chroot /bin/sh \
	&& cd /srv && wget -qO steamcmd_linux.tar.gz https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz \
	&& tar zxvC . -f steamcmd_linux.tar.gz \
	&& ls -la

