FROM caoyufei/debian

ENV Redis_Stable_Ver=redis-6.0.3 TIME_ZONE=Asia/Shanghai

RUN apt-get update -y \
    && apt-get --no-install-recommends install -y build-essential gcc make wget libc6-dev ca-certificates \
    && groupadd redis \
    && useradd -s /sbin/nologin -g redis redis \
    && cd /tmp \
    && wget -c --progress=bar:force --prefer-family=IPv4 --no-check-certificate http://download.redis.io/releases/${Redis_Stable_Ver}.tar.gz \
    && tar zxf ${Redis_Stable_Ver}.tar.gz \
    && cd ${Redis_Stable_Ver} \
    && make PREFIX=/usr/local/redis install \
    && ln -sf /usr/local/redis/bin/redis-server /usr/bin/redis-server \
    && mkdir -p /usr/local/redis/etc/ && mkdir -p /data/redis \
    && chown -R redis:redis /data/redis \
    && cp redis.conf  /usr/local/redis/etc/ \
    && sed -i 's#^dir.*#dir \/data\/redis#g' /usr/local/redis/etc/redis.conf \
    && apt-get purge build-essential gcc make wget libc6-dev ca-certificates -y \
    && apt-get autoremove -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

STOPSIGNAL SIGQUIT

WORKDIR /data/redis

COPY docker-entrypoint.sh /usr/local/bin/
ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 6379
CMD ["redis-server", "/usr/local/redis/etc/redis.conf"]