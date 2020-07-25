FROM caoyufei/debian

ENV  hp_Ver=php-7.2.31 PHPRedis_Ver=redis-5.2.2
ADD conf /tmp/conf

RUN apt-get update -y \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get --no-install-recommends install -y build-essential gcc g++ make autoconf wget libxml2-dev libcurl4-gnutls-dev bison curl ca-certificates re2c libjpeg-dev libpng-dev libfreetype6-dev openssl libssl-dev  \
    && CONFIG="\
    --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --with-config-file-scan-dir=/usr/local/php/conf.d \
    --enable-fpm --with-fpm-user=www --with-fpm-group=www --enable-mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd \
    --with-iconv-dir --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib --with-libxml-dir=/usr --enable-xml \
    --disable-rpath --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --enable-mbregex \
    --enable-mbstring --enable-intl --enable-pcntl --enable-ftp --with-gd --with-openssl --with-mhash --enable-pcntl \
    --enable-sockets --with-xmlrpc --enable-zip --enable-soap --with-gettext --enable-opcache --enable-fileinfo \
    " \
    && groupadd www \
    && useradd -s /sbin/nologin -g www www \
    && cd /tmp \
    && wget -c --progress=bar:force --prefer-family=IPv4 --no-check-certificate http://soft.vpser.net/web/php/${Php_Ver}.tar.bz2 \
    && tar xjf ${Php_Ver}.tar.bz2 \
    && cd /tmp/${Php_Ver} \
    && ./configure $CONFIG \
    && make -j$(getconf _NPROCESSORS_ONLN) \
    && make install \
    && ln -sf /usr/local/php/bin/php /usr/bin/php \
    && ln -sf /usr/local/php/bin/phpize /usr/bin/phpize \
    && ln -sf /usr/local/php/bin/pear /usr/bin/pear \
    && ln -sf /usr/local/php/bin/pecl /usr/bin/pecl \
    && ln -sf /usr/local/php/sbin/php-fpm /usr/bin/php-fpm \
    && cp php.ini-production /usr/local/php/etc/php.ini \
    && mkdir -p /usr/local/php/conf.d \
    && mkdir -p /data/wwwlogs \
    && sed -i 's/post_max_size =.*/post_max_size = 50M/g' /usr/local/php/etc/php.ini \
    && sed -i 's/upload_max_filesize =.*/upload_max_filesize = 50M/g' /usr/local/php/etc/php.ini \
    && sed -i 's/;date.timezone =.*/date.timezone = PRC/g' /usr/local/php/etc/php.ini \
    && sed -i 's/;error_log = php_errors.log/error_log = \/data\/wwwlogs\/php_errors.log/g' /usr/local/php/etc/php.ini \
    && sed -i 's/short_open_tag =.*/short_open_tag = On/g' /usr/local/php/etc/php.ini \
    && sed -i 's/;cgi.fix_pathinfo=.*/cgi.fix_pathinfo=0/g' /usr/local/php/etc/php.ini \
    && sed -i 's/max_execution_time =.*/max_execution_time = 300/g' /usr/local/php/etc/php.ini \
    && sed -i 's/disable_functions =.*/disable_functions = passthru,system,chroot,chgrp,chown,shell_exec,popen,ini_alter,ini_restore,dl,openlog,syslog,readlink,popepassthru,stream_socket_server/g' /usr/local/php/etc/php.ini \
    && pear config-set php_ini /usr/local/php/etc/php.ini \
    && pecl config-set php_ini /usr/local/php/etc/php.ini \
    && pecl update-channels \
    && cp /tmp/conf/php-fpm.conf /usr/local/php/etc/php-fpm.conf \
    && chmod +x /tmp/conf/php-configure && ./tmp/conf/php-configure \
    && rm ~/.pearrc \
    && rm -f /usr/local/php/conf.d/* \
    && cp /tmp/conf/004-opcache.ini /usr/local/php/conf.d/004-opcache.ini \
    && cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm \
    && chmod +x /etc/init.d/php-fpm \
    && cd /tmp \
    && wget -c --progress=bar:force --prefer-family=IPv4 --no-check-certificate http://pecl.php.net/get/${PHPRedis_Ver}.tgz \
    && tar zxf ${PHPRedis_Ver}.tgz \
    && cd /tmp/${PHPRedis_Ver} \
    && /usr/local/php/bin/phpize \
    && ./configure --with-php-config=/usr/local/php/bin/php-config \
    && make \
    && make install \
    && cp /tmp/conf/007-redis.ini /usr/local/php/conf.d/007-redis.ini \
    && wget --prefer-family=IPv4 --no-check-certificate -T 120 -t3 http://soft.vpser.net/web/php/composer/composer.phar -O /usr/local/bin/composer \
    && chmod +x /usr/local/bin/composer \
    && composer global require "fxp/composer-asset-plugin"
    && apt-get purge binutils binutils-common binutils-x86-64-linux-gnu gir1.2-glib-2.0 gir1.2-harfbuzz-0.0 build-essential gcc g++ make wget autoconf python3* cpp cpp-7 g++-7 gcc-7 gcc-7-base -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

STOPSIGNAL SIGQUIT
WORKDIR /data/wwwroot

CMD ["php-fpm", "-F", "--fpm-config", "/usr/local/php/etc/php-fpm.conf"]