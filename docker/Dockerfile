FROM alpine:latest AS build

WORKDIR /tmp
ADD https://keepalived.org/software/keepalived-2.2.8.tar.gz .

RUN set eux; \
  addgroup -S keepalived_script; \
  adduser -D -S -G keepalived_script keepalived_script;

RUN set eux; \
  apk add --update --no-cache \
  binutils \
  file \
  file-dev \
  gcc \
  glib \
  glib-dev \
  ipset \
  ipset-dev \
  iptables \
  iptables-dev \
  libmnl-dev \
  libnftnl-dev \
  libnl3 \
  libnl3-dev \
  make \
  musl-dev \
  net-snmp-dev \
  openssl \
  openssl-dev \
  pcre2 \
  pcre2-dev \
  autoconf \
  automake; \
  tar xvzf keepalived-2.2.8.tar.gz; \
  cd /tmp/keepalived-2.2.8/; \
  ./autogen.sh; \
  ./configure \
  --disable-dynamic-linking \
  --prefix=/usr \
  --exec-prefix=/usr \
  --bindir=/usr/bin \
  --sbindir=/usr/sbin \
  --sysconfdir=/etc \
  --datadir=/usr/share \
  --localstatedir=/var \
  --mandir=/usr/share/man \
  --with-dbus-data-dir=/usr/share \
  --enable-bfd \
  --enable-dbus \
  --enable-regex \
  --enable-snmp \
  --enable-snmp-rfc \
  --enable-nftables \
  --disable-iptables \
  --enable-json; \
  make; \
  make install; \
  strip /usr/sbin/keepalived; \
  cd -; \
  rm -rf /tmp/keepalived-2.2.8; \
  apk --no-cache del \
  binutils \
  file-dev \
  gcc \
  glib-dev \
  ipset-dev \
  iptables-dev \
  libmnl-dev \
  libnl3-dev \
  libnftnl-dev \
  make \
  musl-dev \
  openssl-dev \
  pcre2-dev \
  autoconf \
  automake

FROM alpine:latest
WORKDIR /src/keepalived
COPY keepalived.conf /etc/keepalived/
COPY entrypoint /usr/bin/
RUN set eux; \
  apk add --update --no-cache \
  sudo \
  bash \
  file \
  glib \
  ipset \
  iptables \
  libnl3 \
  net-snmp-dev \
  openssl \
  pcre2; \
  addgroup -S keepalived; \
  adduser -S keepalived -G keepalived -h /src/keepalived -s /bin/bash; \
  addgroup keepalived sudo; \
  chmod a+x /usr/bin/entrypoint; \
  sed -i '/# %sudo/c keepalived ALL=(ALL:ALL) NOPASSWD:/usr/sbin/keepalived' /etc/sudoers; \
  chown -R keepalived:keepalived /etc/keepalived/ /run;
COPY --from=build /usr/sbin/ /usr/sbin/
USER keepalived
ENTRYPOINT ["entrypoint"]
CMD ["sudo", "/usr/sbin/keepalived", "--dont-fork", "--log-console", "--use-file", "/etc/keepalived/keepalived.conf"]
