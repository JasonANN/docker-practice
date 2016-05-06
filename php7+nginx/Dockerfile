From ubuntu:14.04
MAINTAINER jason@ifalo.com.tw	

ENV DEBIAN_FRONTEND noninteractive
ENV TIMEZONE Asia/Taipei

# set time zone
RUN ln -sf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime

# update time
RUN ntpdate time.stdtime.gov.tw

# 0 - action allowed
RUN sed -i 's/^exit.*/exit 0/g' /usr/sbin/policy-rc.d

RUN apt-get update
RUN apt-get install -y python-software-properties software-properties-common
RUN add-apt-repository -y ppa:ondrej/php && apt-get update

RUN apt-get install -y --force-yes build-essential curl git memcached mongodb nginx
RUN apt-get install -y --force-yes php7.0 php7.0-mysql php7.0-fpm php7.0-cli php7.0-common php7.0-curl php7.0-gd php7.0-intl php7.0-json php7.0-mbstring php7.0-mcrypt php7.0-opcache php7.0-pgsql php7.0-soap php7.0-sqlite3 php7.0-xml php7.0-xmlrpc php7.0-xsl php7.0-zip

# modified php7.0-fpm conf
RUN sed -i 's/^listen =.*/listen = \/var\/run\/php\/php7.0-fpm\.sock/g' /etc/php/7.0/fpm/pool.d/www.conf && \
    sed -i 's/^;listen.owner =/listen.owner =/g' /etc/php/7.0/fpm/pool.d/www.conf && \
    sed -i 's/^;listen.group =/listen.group =/g' /etc/php/7.0/fpm/pool.d/www.conf && \
    sed -i 's/^;listen.mode =/listen.mode =/g' /etc/php/7.0/fpm/pool.d/www.conf && \
    sed -i 's/^;clear_env =/clear_env =/g' /etc/php/7.0/fpm/pool.d/www.conf 

# modified php.ini
RUN sed -i 's/^;date.timezone =.*/date.timezone = ${TIMEZONE}/g' /etc/php/7.0/fpm/php.ini
RUN sed -i "s/^memory_limit =.*/memory_limit = 4096M/g" /etc/php/7.0/fpm/php.ini && \
    sed -i "s/^max_execution_time =.*/max_execution_time = 600/g" /etc/php/7.0/fpm/php.ini && \
    sed -i "s/^error_reporting =.*/error_reporting = E_ALL \| E_STRICT/g" /etc/php/7.0/fpm/php.ini && \
    sed -i "s/^display_errors =.*/display_errors = On/g" /etc/php/7.0/fpm/php.ini && \
    sed -i "s/^display_startup_errors =.*/display_startup_errors = On/g" /etc/php/7.0/fpm/php.ini

# install composer
RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer

RUN phpenmod mcrypt
RUN apt-get install -y supervisor
# clear apt cache and remove unnecessary packages
RUN apt-get autoclean && apt-get -y autoremove

RUN echo "<?php phpinfo();" >> /usr/share/nginx/html/index.php

RUN mv /etc/nginx/sites-available/default /etc/nginx/sites-available/default.back
COPY ./default /etc/nginx/sites-enabled/default

RUN service php7.0-fpm restart
VOLUME ["/var/log/nginx","/var/log"]
CMD /bin/bash -c "service php7.0-fpm start; nginx -g 'daemon off;'"
EXPOSE 80 443 9000





