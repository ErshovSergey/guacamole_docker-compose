#FROM guacamole/guacd:latest
FROM guacamole/guacd:1.4.0

USER root
RUN  \
  apt-get update -qy \
  && apt-get install -yqq ghostscript \
  && apt-get autoremove -qqy \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* /tmp/*

