# Use phusion/baseimage as base image. To make your builds
# reproducible, make sure you lock down to a specific version, not
# to `latest`! See
# https://github.com/phusion/baseimage-docker/blob/master/Changelog.md
# for a list of version numbers.
#
# This file is based on https://github.com/mrohland/gocd-docker/ 
# 

FROM phusion/baseimage:0.9.16

MAINTAINER marufaytekin

# Use baseimage-docker's init system.
CMD ["/sbin/my_init"]

# create go user & volumes for go.cd
RUN ["groupadd", "-r", "go"]
RUN ["useradd", "-r", "-c", "Go User", "-g", "go", "-d", "/var/go", "-m", "-s", "/bin/bash", "go"]
RUN ["mkdir", "-p", "/var/lib/go-server/addons","/var/lib/go-server/plugins/external", "/var/log/go-server", "/etc/go", "/go-addons"]
RUN ["chown", "-R", "go:go", "/var/lib/go-server", "/var/log/go-server", "/etc/go", "/go-addons"]
VOLUME ["/var/lib/go-server","/var/lib/go-server/plugins/external", "/var/log/go-server", "/etc/go", "/go-addons"]

# Clean up APT when done.
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
