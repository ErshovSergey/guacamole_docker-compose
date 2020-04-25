FROM guacamole/guacd

RUN  \
  apt-get update -qqy \
  && apt-get install -yqq ghostscript \
  && apt-get autoremove -qqy \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* /tmp/*

