FROM alpine:3.4

ENV GOSU_VERSION=1.9 \
 LANG=en_US.utf8 \
 PGDATA=/var/lib/postgresql/data

RUN set -x \
 && sed -i -e "s/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g" /etc/apk/repositories \
 && apk update && apk add  --no-cache --virtual .gosu-deps dpkg gnupg openssl musl-dev make gcc curl \
 && dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')" \
 && wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch" \
 && wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch.asc" \
 && export GNUPGHOME="$(mktemp -d)" \
 && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
 && gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu \
 && curl -sSL https://github.com/Yelp/dumb-init/archive/master.tar.gz | tar -xz \
 && cd dumb-init-master && gcc -std=gnu99 -s -Wall -Werror -O3 -o /usr/local/bin/dumb-init /dumb-init-master/dumb-init.c \
 && rm -r "$GNUPGHOME" /usr/local/bin/gosu.asc /dumb-init-master \
 && chmod +x /usr/local/bin/gosu \
 && apk add postgresql \
 && mkdir /docker-entrypoint-initdb.d \
 && apk del .gosu-deps \
 && rm -rf /var/cache/apk/*

COPY docker-entrypoint.sh /docker-entrypoint.sh
VOLUME /var/lib/postgresql/data

EXPOSE 5432

ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["postgres"]
